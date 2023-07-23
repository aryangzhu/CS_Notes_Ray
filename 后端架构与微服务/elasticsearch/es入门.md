# 简介
一个分布式的全文搜索引擎
# docker安装
docker pull elasticsearch:6.4.0  
docker命令创建容器  
docker run -p 9200:9200 -p 9300:9300 --name elasticsearch \
-e "discovery.type=single-node" \
-e "cluster.name=elasticsearch" \
-v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-v /mydata/elasticsearch/data:/usr/share/elasticsearch/data \
-d elasticsearch:6.4.0  
安装中文分词器  
docker exec -it elasticsearch /bin/bash
此命令需要在容器中运行  
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.4.0/elasticsearch-analysis-ik-6.4.0.zip
docker restart elasticsearch
# docker安装Kibana
kibana相当于访问es的客户端
# 相关概念
Index:索引时一些具有相似特征的文档集合,类似于MySql数据库的概念。
Type:类型是索引的逻辑分区,类似于表
Document: 文档是可被索引的基本单位,类似于行记录。
# 实践
执法平台法律法规全文搜索
Controller层
```
if(StrUtils.isNotNull(text)){
                queryBuilder.must((QueryBuilders.multiMatchQuery(text,"name","content")));
            }

            if(StrUtils.isNotNull(isTime)){
                queryBuilder.must((QueryBuilders.termQuery("isTime",isTime)));
            }
            if(StrUtils.isNotNull(directoryid)){
                queryBuilder.must((QueryBuilders.termQuery("directoryId",Long.valueOf(directoryid))));
            }
            if(StrUtils.isNotNull(createdTime)){
                queryBuilder.must((QueryBuilders.matchQuery("createdTime",createdTime)));
            }
            esQueryReqPO.setQuery(queryBuilder);
            esQueryReqPO.setSortField("updatedTime");
            esQueryReqPO.setSort(SortOrder.DESC);
            esQueryReqPO.setPageNum(Integer.valueOf(page));
            esQueryReqPO.setPageSize(Integer.valueOf(limit));
            esQueryReqPO.setHighLighting("name","content");
            EsQueryRespPO esQueryRespPO = esRepository.pageSearch(esQueryReqPO);
            List<Map<String, Object>> sourceList = esQueryRespPO.getSourceList();
```
Service层
```
public EsQueryRespPO pageSearch(EsQueryReqPO queryPO) {
        // 默认分页参数设置
        if (queryPO.getPageNum() == null) {
            queryPO.setPageNum(1);
        }
        if (queryPO.getPageSize() == null) {
            queryPO.setPageSize(10);
        }
//        queryPO.getQuery();
        // 封装查询源对象
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();



        String[] highLighting = queryPO.getHighLighting();
        if(highLighting!=null&&highLighting.length>0){
            HighlightBuilder highlightBuilder = new HighlightBuilder();
            highlightBuilder.numOfFragments(1);
            highlightBuilder.fragmentSize(100); /*长度*/
            highlightBuilder.preTags("<em style='color:#F5222D;font-style: normal;'>");
            highlightBuilder.postTags("</em>");
            List<HighlightBuilder.Field> fields = highlightBuilder.fields();
            for (String s : highLighting) {
                fields.add(new HighlightBuilder.Field(s));
            }
            /*需要highlightBuilder，前面创建好*/
            sourceBuilder.highlighter(highlightBuilder);
        }
        // 查询条件
        sourceBuilder.query(queryPO.getQuery());

        // 排序字段
        if (StringUtils.isNotBlank(queryPO.getSortField()) && queryPO.getSort() != null) {
            FieldSortBuilder order = new FieldSortBuilder(queryPO.getSortField()).order(queryPO.getSort());
            sourceBuilder.sort(order);
        }



        // 开始行数，默认0
        sourceBuilder.from((queryPO.getPageNum() - 1) * queryPO.getPageSize());
        // 页大小，默认10
        sourceBuilder.size(queryPO.getPageSize());

        // 设置索引、source
        SearchRequest searchRequest = new SearchRequest(queryPO.getIndex()).source(sourceBuilder);



        // 查询结果
        SearchResponse searchResponse = null;
        try {
            logger.info("es分页查询请求：index={}, 请求source={}", queryPO.getIndex(), searchRequest.source());
            searchResponse = highLevelClient.search(searchRequest, RequestOptions.DEFAULT);
            //logger.info("es分页查询结果：{}", searchResponse);
        } catch (IOException e) {
            logger.error("es查询，IO异常！es查询index={}, 请求source={}", queryPO.getIndex(), searchRequest.source(), e);
            throw new EsException("es查询，IO异常！");
        }

        if (RestStatus.OK.equals(searchResponse.status())) {
            // 解析对象
           // SearchHit[] hits = searchResponse.getHits().getHits();
            List<Map<String, Object>> sourceList=new ArrayList<>();
//            String monitoringStation="加强";
            /*map转对象(中间有jsonString做中间转换)*/
            for (SearchHit hit : searchResponse.getHits().getHits()) {
                /*es中获取的数据是map集合的形式*/
                Map<String, Object> sourceAsMap = hit.getSourceAsMap();

                /*得到高亮的对象的string*/
                Map<String, HighlightField> highlightFields = hit.getHighlightFields();

                highlightFields.forEach((key,value)->{
                    Text[] fragments = value.fragments();
                    StringBuilder new_name = new StringBuilder();
                    for (Text text : fragments) {
                        new_name.append(text);
                    }
                    sourceAsMap.put(key,new_name.toString());
                });

                /*防止空指针异常*/
                // 替换
//                if (highlightField != null){
//                    Text[] fragments = highlightField.fragments();
//                    StringBuilder new_name = new StringBuilder();
//                    for (Text text : fragments) {
//                        new_name.append(text);
//                    }
//                    sourceAsMap.put("content",new_name.toString());
//                }
                /*将map转json，json转对象*/
//                String jsonString = JSON.toJSONString(sourceAsMap);
//                Air air = JSON.parseObject(jsonString, Air.class);
//                list.add(air);
                sourceList.add(sourceAsMap);
            }

            // 获取source
//            List<Map<String, Object>> sourceList = Arrays.stream(hits).map(SearchHit::getSourceAsMap).collect(Collectors.toList());
            long totalHits = searchResponse.getHits().getTotalHits().value;
            return new EsQueryRespPO(queryPO.getPageNum(), queryPO.getPageSize(), totalHits, sourceList);
        } else {
            logger.error("es查询返回的状态码异常！searchResponse.status={}, index={}, 请求source={}", searchResponse.status(),
                    queryPO.getIndex(), searchRequest.source());
            throw new EsException("es查询返回的状态码异常");
        }

    }
```

运行redis容器
docker run -p 6379:6379 --name redis -v /Users/liulei/workspace/data/redis/data:/data -v /Users/liulei/workspace/data/redis/redis.conf:/etc/redis/redis.conf -d redis redis-server /etc/redis/redis.conf

运行es容器
mkdir /software/elasticsearch
mkdir config
mkdir data
mkdir plugins

echo "http.host: 0.0.0.0" >> /Users/liulei/software/elasticsearch/elasticsearch.yml


docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx512m" \
-v /Users/liulei/software/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /Users/liulei/software/elasticsearch/data:/usr/share/elasticsearch/data \
-v /Users/liulei/software/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.16.2

安装分词器  
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.16.2/elasticsearch-analysis-ik-7.16.2.zip