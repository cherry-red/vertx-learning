# vertx-learning
vertx源码分析

## 坑
### jdbc client 共用一个线程池，且线程池里只有一个线程，导致高并发下，所有的数据库阻塞在获取连接上，响应时间、tps降低。
解决方案：使用createNonShared，创建jdbc连接，每个Verticle拥有自己的线程池。压测发现，响应时间提高近7倍，tps提高三倍多。

#### 使用路径：
- JDBCClient.createNonShared（创建jdbc client） -> getConnection（获取连接） -> execute（执行curd）
#### 过程解析
- JDBCClient.createNonShared（创建jdbc client）
- getConnection（获取连接）：共享线程池，线程池里只有一个线程
- execute（执行curd）：共享worker线程池，线程池里线程数可以配置

### redis client queue有数量限制，在高并发下会出现 redis is full异常

### websocket无法判断失败原因。
