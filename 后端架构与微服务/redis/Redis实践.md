### 分布式ID生成器
自定义接口
```Java
@Component
public class DistributeSnowFlakeIdGenerator implements IdGenerator {

	private Sequence sequence;

	@Autowired
	RedisCache redisCache;

	// id生成器进程标识注册之缓存key
	private final static String CACHE_REGISTRY_KEY = "ID_GENERATOR_FLAG_REGISTRY";

	// 服务节点注册时间上限,超过此时间则认为此节点已死
	final static int REGISTER_MAX_HOURS = 3;

	// 本进程的id生成器进程标识键
	private static Long FLAG_ITEM_KEY = -1L;
	//注册尝试最大次数
	private final static int REGISTER_COUNT = 50;

	private static Logger log = LoggerFactory.getLogger(DistributeSnowFlakeIdGenerator.class);

	@PostConstruct
	public void init() {

		removeDeadIdGenerateNodes(redisCache.hmget(CACHE_REGISTRY_KEY));
		registerKey();

		Long dataCenterId = FLAG_ITEM_KEY / 32;
		Long realWorkerId = FLAG_ITEM_KEY % 32;
		sequence = new Sequence(realWorkerId, dataCenterId);
	}

	/**
	 * 定时刷新本节点的注册值,报告本节点还活着(2小时一次)
	 */
	@Scheduled(cron = "0 0 0/2 * * ? ")
	public void refreshFlagRegistry() {
		redisCache.hset(CACHE_REGISTRY_KEY, FLAG_ITEM_KEY.toString(), String.valueOf(System.currentTimeMillis()));
	}
	
	/**
	 * 注册本节点到缓存中
	 */
	void registerKey() {
		Map<Object, Object> registries = null;
		Long candidateWorkId = null;
		boolean result = false;
		//重复注册一定次数
		for (int i = 0; i < REGISTER_COUNT; i++) {
			registries = redisCache.hmget(CACHE_REGISTRY_KEY);
			candidateWorkId = getCandidateWorkId(registries);
			result = redisCache.hsetIfAbsent(CACHE_REGISTRY_KEY, candidateWorkId.toString(), String.valueOf(System.currentTimeMillis()));
			if(result) {
				// 本地缓存竞争到的标识,以便定期刷新
				FLAG_ITEM_KEY = candidateWorkId;
				log.info("id生成器redis注册标识{}成功",FLAG_ITEM_KEY);
				break;
			}
		}
		
		if(!result) {
			log.error("id生成器redis注册失败");
			throw new RuntimeException("id生成器redis注册失败");
		}
	}

	/**
	 * 获得本节点可使用的占位标识
	 * @param registries  redis里注册的节点
	 * @return  本节点可使用的占位标识
	 */
	Long getCandidateWorkId(Map<Object, Object> registries) {
		if(registries == null || registries.size() == 0)
			return 1L;

		Integer id = getUnusedId(registries);
		if(id < 0)
			id = collectIdFromExpiredNodes(registries);

		if(id < 0) {
			log.error("雪花id生成器找不到可用的占位标识,进程退出");
			throw new RuntimeException("id生成器redis注册失败");
		}

		return Long.valueOf(id);
	}
	
	/**
	 * 从过期节点中收集可使用占位标识
	 * 已过期节点认为是死亡节点
	 * @param registries 占位标识注册信息
	 * @return 收集到的可使用的过期占位标识
	 */
	Integer collectIdFromExpiredNodes(Map<Object, Object> registries){
		if (registries == null || registries.size() == 0)
			return -1;
		
		Integer id = -1;
		for (Object itemKey : registries.keySet()) {
			String itemValue = registries.get(itemKey).toString();

			Long registerDays = (System.currentTimeMillis() - Long.valueOf(itemValue)) / (1000 * 3600 * 3);
			if (registerDays > REGISTER_MAX_HOURS) {
				id = Integer.valueOf(itemKey.toString());
				break;
			}
				
		}
		
		return id;
	}
	
	/**
	 * 获得尚未使用的占位标识
	 * @param registries
	 * @return
	 */
	Integer getUnusedId(Map<Object, Object> registries) {
		if (registries == null || registries.size() == 0)
			return 1;
		
		List<Integer> unusedIds = new ArrayList<Integer>(40);

		for (Object itemKey : registries.keySet()) {
			unusedIds.add(Integer.valueOf(itemKey.toString()));
		}

		int count = unusedIds.size();
		if (count == 1)
			return unusedIds.get(0) <= 1022 ? unusedIds.get(0) + 1 : 1;

		unusedIds.sort(new Comparator<Integer>() {
			@Override
			public int compare(Integer o1, Integer o2) {
				return o1 - o2;
			}
		});
		
		Integer minUsed = unusedIds.get(0);
		if(minUsed > 1)
			return minUsed - 1;
		Integer maxUsed = unusedIds.get(count -1);
		if(maxUsed < 1023)
			return maxUsed + 1;
		
		Integer unusedId = -1;
		for (int i = 0; i < count - 1; i++) {
			if (unusedIds.get(i + 1) == unusedIds.get(i) + 1)
				continue;

			unusedId = unusedIds.get(i) + 1;
			break;
		}
		
		return unusedId;
	}
	
	/**
	 * 删除判断为死亡的节点的占位标识,为新节点注册占位标识提供可用资源
	 * @param registries redis里注册的节点
	 */
	void removeDeadIdGenerateNodes(Map<Object, Object> registries) {
		Set<Object> nodes = getDeadIdGenerateNodes(registries);
		if(nodes.size() == 0)
			return;
		
		redisCache.hdel(CACHE_REGISTRY_KEY, nodes.toArray());
		log.info("清除redis中的死亡节点的占位标识：{}",nodes.toString());
	}

	/**
	 * 搜集死亡的节点
	 * @param registries redis里注册的节点
	 * @return 死亡的节点key集合
	 */
	Set<Object> getDeadIdGenerateNodes(Map<Object, Object> registries) {
		Set<Object> itemKeys = new HashSet<Object>(40);
		for (Object itemKey : registries.keySet()) {
			String itemValue = registries.get(itemKey).toString();

			Long registerDays = (System.currentTimeMillis() - Long.valueOf(itemValue)) / (1000 * 3600 * 3);
			if (registerDays >= REGISTER_MAX_HOURS)
				itemKeys.add(itemKey);
		}
		
		return itemKeys;
	}

	@Override
	public Long generate() {
		return sequence.nextId();
	}

}
```
有序id序列类
```Java
/**
 * 基于Twitter的Snowflake算法实现分布式高效有序ID生产黑科技(sequence)
 * 
 * <br>
 * SnowFlake的结构如下(每部分用-分开):<br>
 * <br>
 * 0 - 0000000000 0000000000 0000000000 0000000000 0 - 00000 - 00000 - 000000000000 <br>
 * <br>
 * 1位标识，由于long基本类型在Java中是带符号的，最高位是符号位，正数是0，负数是1，所以id一般是正数，最高位是0<br>
 * <br>
 * 41位时间截(毫秒级)，注意，41位时间截不是存储当前时间的时间截，而是存储时间截的差值（当前时间截 - 开始时间截)
 * 得到的值），这里的的开始时间截，一般是我们的id生成器开始使用的时间，由我们程序来指定的（如下下面程序IdWorker类的startTime属性）。41位的时间截，可以使用69年，年T = (1L << 41) / (1000L * 60 * 60 * 24 * 365) = 69<br>
 * <br>
 * 10位的数据机器位，可以部署在1024个节点，包括5位datacenterId和5位workerId<br>
 * <br>
 * 12位序列，毫秒内的计数，12位的计数顺序号支持每个节点每毫秒(同一机器，同一时间截)产生4096个ID序号<br>
 * <br>
 * <br>
 * 加起来刚好64位，为一个Long型。<br>
 * SnowFlake的优点是，整体上按照时间自增排序，并且整个分布式系统内不会产生ID碰撞(由数据中心ID和机器ID作区分)，并且效率较高，经测试，SnowFlake每秒能够产生26万ID左右。
 * 
 * @author lry
 */
public class Sequence {

	/** 开始时间截 */
	private final long twepoch = 1288834974657L;
	/** 机器id所占的位数 */
	private final long workerIdBits = 5L;
	/** 数据标识id所占的位数 */
	private final long datacenterIdBits = 5L;
	/** 支持的最大机器id，结果是31 (这个移位算法可以很快的计算出几位二进制数所能表示的最大十进制数) */
	private final long maxWorkerId = -1L ^ (-1L << workerIdBits);
	/** 支持的最大数据标识id，结果是31 */
	private final long maxDatacenterId = -1L ^ (-1L << datacenterIdBits);
	/** 序列在id中占的位数 */
	private final long sequenceBits = 12L;
	/** 机器ID向左移12位 */
	private final long workerIdShift = sequenceBits;
	/** 数据标识id向左移17位(12+5) */
	private final long datacenterIdShift = sequenceBits + workerIdBits;
	/** 时间截向左移22位(5+5+12) */
	private final long timestampLeftShift = sequenceBits + workerIdBits + datacenterIdBits;
	/** 生成序列的掩码，这里为4095 (0b111111111111=0xfff=4095) */
	private final long sequenceMask = -1L ^ (-1L << sequenceBits);

	/** 工作机器ID(0~31) */
	private long workerId;
	/** 数据中心ID(0~31) */
	private long datacenterId;
	/** 毫秒内序列(0~4095) */
	private long sequence = 0L;
	/** 上次生成ID的时间截 */
	private long lastTimestamp = -1L;

	/**
	 * @param workerId 工作ID (0~31)
	 * @param datacenterId 数据中心ID (0~31)
	 */
	public Sequence(long workerId, long datacenterId) {
		if (workerId > maxWorkerId || workerId < 0) {
			throw new IllegalArgumentException(String.format("worker Id can't be greater than %d or less than 0", maxWorkerId));
		}
		
		if (datacenterId > maxDatacenterId || datacenterId < 0) {
			throw new IllegalArgumentException(String.format("datacenter Id can't be greater than %d or less than 0", maxDatacenterId));
		}
		
		this.workerId = workerId;
		this.datacenterId = datacenterId;
	}

	/**
	 * 获得下一个ID (该方法是线程安全的)
	 * 
	 * @return
	 */
	public synchronized long nextId() {
		long timestamp = timeGen();

		// 如果当前时间小于上一次ID生成的时间戳，说明系统时钟回退过这个时候应当抛出异常
		if (timestamp < lastTimestamp) {// 闰秒
			long offset = lastTimestamp - timestamp;
			if (offset <= 5) {
				try {
					wait(offset << 1);
					timestamp = timeGen();
					if (timestamp < lastTimestamp) {
						throw new RuntimeException(String.format("Clock moved backwards.  Refusing to generate id for %d milliseconds", offset));
					}
				} catch (Exception e) {
					throw new RuntimeException(e);
				}
			} else {
				throw new RuntimeException(String.format("Clock moved backwards.  Refusing to generate id for %d milliseconds", offset));
			}
		}
		
		//$NON-NLS-解决跨毫秒生成ID序列号始终为偶数的缺陷$
		// 如果是同一时间生成的，则进行毫秒内序列
		if (lastTimestamp == timestamp) {
			sequence = (sequence + 1) & sequenceMask;
			// 毫秒内序列溢出
			if (sequence == 0) {
				// 阻塞到下一个毫秒,获得新的时间戳
				timestamp = tilNextMillis(lastTimestamp);
			}
		} else {// 时间戳改变，毫秒内序列重置
			sequence = 0L;
		}
		/**
		// 如果是同一时间生成的，则进行毫秒内序列
		if (lastTimestamp == timestamp) {
		    long old = sequence;
		    sequence = (sequence + 1) & sequenceMask;
		    // 毫秒内序列溢出
		    if (sequence == old) {
		        // 阻塞到下一个毫秒,获得新的时间戳
		        timestamp = tilNextMillis(lastTimestamp);
		    }
		} else {// 时间戳改变，毫秒内序列重置
		    sequence = ThreadLocalRandom.current().nextLong(0, 2);
		}
		**/

		// 上次生成ID的时间截
		lastTimestamp = timestamp;

		// 移位并通过或运算拼到一起组成64位的ID
		return ((timestamp - twepoch) << timestampLeftShift) //
				| (datacenterId << datacenterIdShift) //
				| (workerId << workerIdShift) //
				| sequence;
	}

	/**
	 * 阻塞到下一个毫秒，直到获得新的时间戳
	 * 
	 * @param lastTimestamp 上次生成ID的时间截
	 * @return 当前时间戳
	 */
	protected long tilNextMillis(long lastTimestamp) {
		long timestamp = timeGen();
		while (timestamp <= lastTimestamp) {
			timestamp = timeGen();
		}
		
		return timestamp;
	}

	/**
	 * 返回以毫秒为单位的当前时间
	 * 
	 * @return 当前时间(毫秒)
	 */
	protected long timeGen() {
		return SystemClock.now();
	}

}
```