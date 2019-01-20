#mybatis 



- 一级缓存(默认开启)

1. sqlsession级别的
2. 怎么验证有一级缓存

二级缓存

mapper级别的缓存

存在的问题

1. 连表时候，副mapper更新不会联动更新主mapper的被关联表缓存。即脏数据
2. 全部失效，缓存id 1、2、3数据，update id=1，全清空，不细致。