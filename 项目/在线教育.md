## 技术架构
SpringCloud、SpringCloud Alibaba
**新名词**
**DBLE**,基于阿里的**MyCat**组件,用来起分库分表的作用  
**Canal**,MySQL的binlog的增量订阅和消费组件  
## 亮点
### 1.限流
#### 大网关NGINX限流
#### SpringCloud网关限流
#### 使用Redis来做限流RateLimiter
限流工具切面,后面我又用拦截器基于令牌桶算法实现了拦截器模式的方案
```java
package com.yizhi.system.application.limiter.aspect;

import com.yizhi.core.application.exception.BizException;
import com.yizhi.system.application.config.LimiterConfiguration;
import com.yizhi.system.application.limiter.RateLimiter;
import org.apache.commons.lang3.StringUtils;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.core.script.DefaultRedisScript;
import org.springframework.data.redis.core.script.RedisScript;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.ThreadLocalRandom;

@Aspect
@Component
public class RateLimiterAspect {

    private static final Logger log = LoggerFactory.getLogger(RateLimiterAspect.class);
    @Autowired
    private LimiterConfiguration limiterConfiguration;

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Resource
    private RedisScript<Long> limitScript;

    @Bean
    public RedisScript<Long> limitScript() {
        String luaScript = buildLuaScript();
        RedisScript<Long> redisScript = new DefaultRedisScript<>(luaScript, Long.class);
        return redisScript;
    }

    @Before("@annotation(rateLimiter)")
    public void doBefore(JoinPoint point, RateLimiter rateLimiter) throws Throwable {
        if (limiterConfiguration.getLimiterSwitch() == 0) {
            return;
        }
        //限流Key
        String key = rateLimiter.key();
        //限流时间（秒）
        //int time = rateLimiter.time();
        int time = limiterConfiguration.getTime();
        //限流次数
        //int count = rateLimiter.count();
        int count = limiterConfiguration.getCount();
        //重试次数
        //int retry = rateLimiter.retry();
        int retry = limiterConfiguration.getRetry();
        //重试间隔时间（毫秒）
        int intervalTime = rateLimiter.intervalTime();
        try {
            List<String> keys = Collections.singletonList(getCombineKey(rateLimiter, point));
            Long number = stringRedisTemplate.execute(limitScript, keys, String.valueOf(count), String.valueOf(time));
            if (number == null || number.intValue() > count) {
                //如果限流了，则重试retry次，每次间隔intervalTime毫秒
                boolean rateFlag = false;
                for (int i = 0; i < retry; i++) {
                    Long retryRedis = stringRedisTemplate.execute(limitScript, keys, String.valueOf(count), String.valueOf(time));
                    if (retryRedis == null || retryRedis.intValue() > count) {
                        //线程停顿
                        Thread.sleep(intervalTime + getRangeRandom(50, 100));
                        rateFlag = true;
                    } else {
                        rateFlag = false;
                        break;
                    }
                }
                //如果没有拿到令牌，则直接退出
                if (rateFlag) {
                    log.info("RateLimiterAspect 访问被拒绝 限制请求次数：{}, 当前请求次数：{}, 缓存Key：{}", count, number.intValue(), key);
                    throw new BizException("8888", "访问被拒绝 限制请求");
                }
            }
        } catch (Exception e) {
            throw new BizException("8888", "服务器限流异常，请稍候再试");
        }
    }

    public String getCombineKey(RateLimiter rateLimiter, JoinPoint point) {
        StringBuffer stringBuffer = new StringBuffer();
        //第一层以项目名分包
        String appName = "system";
        if (StringUtils.isNotBlank(appName)) {
            stringBuffer.append(appName).append(":");
        }
        //第二层以rate_limit命名分包，并放到同一个节点
        stringBuffer.append("{rate_limit}:");
        //第三层以限流名定义Key
        stringBuffer.append(rateLimiter.key());
        return stringBuffer.toString();
    }

    /**
     * Lua脚本
     * 1、首先获取到传进来的 key 以及 限流的 count 和时间 time。
     * 2、通过 get 获取到这个 key 对应的值，这个值就是当前时间窗内这个接口可以访问多少次。
     * 3、如果是第一次访问，此时拿到的结果为 nil，否则拿到的结果应该是一个数字，所以接下来就判断，
     * 如果拿到的结果是一个数字，并且这个数字还大于 count，那就说明已经超过流量限制了，那么直接返回查询的结果即可。
     * 4、如果拿到的结果为 nil，说明是第一次访问，此时就给当前 key 自增 1，然后设置一个过期时间。
     * 5、最后把自增 1 后的值返回就可以了。
     *
     * @return
     */
    private static final String buildLuaScript() {
        StringBuilder lua = new StringBuilder();
        lua.append(" local key = KEYS[1]");
        lua.append("\nlocal count = tonumber(ARGV[1])");
        lua.append("\nlocal time = tonumber(ARGV[2])");
        lua.append("\nlocal current = redis.call('get', key)");
        lua.append("\nif current and tonumber(current) > count then");
        lua.append("\n    return tonumber(current)");
        lua.append("\nend");
        lua.append("\ncurrent = redis.call('incr', key)");
        lua.append("\nif tonumber(current) == 1 then");
        lua.append("\n    redis.call('expire', key, time)");
        lua.append("\nend");
        lua.append("\nreturn tonumber(current)");
        return lua.toString();
    }

    /**
     * 获取范围内的随机数
     *
     * @param min 最小数
     * @param max 最大数
     * @return
     */
    public static int getRangeRandom(int min, int max) {
        ThreadLocalRandom random = ThreadLocalRandom.current();
        return random.nextInt(max) % (max - min + 1) + min;
    }

}
```
#### SpringCloud结合Hystrix实现限流
#### Sential组件实现限流
## 技术积累
#### 1. Mapper.xml中做1对多关联查询
这个倒不是什么很复杂的技术，单纯是因为我用的少，记录一下，后面可能还会用到
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yizhi.application.mapper.TrExamAnswerMapper">
    <!-- 考试题目和考试题目选项，一对多 -->
    <resultMap id="answerQuestionDetailMap" type="com.yizhi.exam.application.vo.AnswerQuestionDetailVO">
        <collection property="options"
                    ofType="com.yizhi.exam.application.vo.AnswerQuestionListVO"
                    column="{answer_question_id=answer_question_id,answerId=answerId}"
                    select="answerQuestionDetails">
        </collection>
    </resultMap>

    <select id="answerQuestionDetail" resultMap="answerQuestionDetailMap"
            resultType="com.yizhi.exam.application.vo.AnswerQuestionDetailVO">
        select a.exam_id,
               a.id                                                                                           as answerId,
               c.analysis,
               c.type,
               c.stem,
               c.stem_appendix_url,
               c.id                                                                                           as subject_id,
               c.subject_id                                                                                   as relSubjectId,
               b.id                                                                                           AS answer_question_id,
               b.id                                                                                           AS answerQuestionId,
               c.auto_feedback_flag                                                                           AS autoFeedbackFlag,
               c.correct_answer_feedback                                                                      AS correctAnswerFeedback,
               c.incorrect_answer_feedback                                                                    AS incorrectAnswerFeedback,
               CASE WHEN a.state = 2 THEN b.score ELSE CASE WHEN c.type!=4 THEN b.score ELSE '批阅中' END END AS score,
               b.sort,
               b.mark
        from tr_exam_answer a
                 left join tr_exam_answer_question b
                           on b.company_id = #{companyId} AND a.id = b.answer_id
                 left join tr_question_library_subject c on c.id = b.subject_id
        WHERE a.id = #{answerId}
          and b.company_id = #{companyId}
          and c.type_reading != 51
        order by b.sort, c.create_time, c.id
    </select>

    <select id="answerQuestionDetails" parameterType="java.util.Map"
            resultType="com.yizhi.exam.application.vo.AnswerQuestionListVO">
        select e.content,
               e.option_appendix_url,
               e.is_answer    as studentAnswer,
               d.answer,
               e.sort,
               e.id           as optionId,
               d.blank_answer as blankAnswer,
               e.blank_answer as optionBlankRightAnswer,
               d.right_answer as blankRightAnswer
        from tr_question_subject_option e
                 left join tr_exam_answer_question_res d
                           on d.answer_id = #{answerId} AND e.id = d.option_id
        where d.answer_question_id = #{answer_question_id}
          and d.answer_id = #{answerId}
        order by d.sort
    </select>
</mapper>
```