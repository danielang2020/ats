1、clearing/account/position使用queue通讯，按顺序处理请求。基于event-source。  
2、clearing不处理如果余额不足转负债场景，account处理。避免clearing需要查询两次account获取最新账户信息或者在clearing内存计算。一切以account计算为主。  
3、clearing只负责金额对应科目的选择或者组合。  
4、幂等，调用方传唯一标识结合发起时间，缓存5分钟。SQS=5mins,clearing=8mins 超过五分钟的请求一律丢弃。(幂等主要处理因为框架或者其他非逻辑原因导致的重试)  
5、为避免账务错误，如果处理时，发现异常，直接暂停账户处理（DLQ)。稍后修复后，可以重新消费DLQ  
6、有持仓情况下，不能修改任何产品业务参数，持仓不用考虑开仓时和平仓时存在不同参数场景  
7、因为是one by one，所以无需在进行出入账务前，操作冻结解冻科目。也无需加锁处理账务。lock-free ：账务上如果需要冻结金额，直接划入冻结科目；技术上无需任何lock。  
8、服务要轻量级，便于快速重启继续消费  
9、cap通用逻辑不能参杂玩法逻辑（account有合约逐仓、合约全仓开户特殊逻辑；流水处理有maker特殊逻辑；填充首次入金/出金时间）  
10、最小颗粒需要按照customer维度建立queue进行消费处理，存在跨账户账务处理  
11、定时清理超过3个月的平仓position记录、注销account记录  
12、cap最主要的是能按照用户量进行服务扩容，前提需要优化存储，查询快，服务扩容才能有意义  
13、在目前不修改数据存储的情况下，主库要缩小可提供查询的条件，例如account只能根据customer+tradeType或accountId，position只能根据account + symbol或positionId，复杂查询要么去replica或者data warehouse  
14、cap属于被动服务，以事件驱动为主，除去本身的相关参数需要关注，其他参数应都有玩法服务判断校验或通过接口传给cap  
15、账户清零和公司行动，是单独的通过正常的划账任务去干预。不是处理正常账务时，顺便自动处理。cap要保证正确干净的账务加减逻辑。
16、mq消费维度先按照公司+固定数值（客户组）+玩法，后续如果用户量大，可以调整为公司 + 指定客户组 + 玩法消费。a dead letter queue per company.

account  
1、存在跨两个白标公司账务处理？  
2、凭证登记无需记录数据库，凭证主要的作用是避免重启或者其他原因导致account不可用时，正常后可以继续处理，MQ天然支持。  
3、现在的开户是有账务处理的时候，才开？不应该是用户在界面上手动点击开启什么玩法？如果需要，开户也要跟账务处理逻辑分开  
4、流水id需要顺序的原因？  
5、流水账务持久化？只看到添加ignite逻辑。（write-through？）  
6、自动借款，负债还款，应该属于通用account逻辑的一部分，如果要区分，以请求参数的方式调用account。  
   abcc：借款额度、总授信、是否可借、全仓账户币种(usdt/hkd/usd)[信用卡借款，币种是跟开卡币种一致，再换汇成其他币种，还款也是还开卡币种]  
7、账务处理分两类，把一笔资金做逻辑划分到不同科目，便于账务分析和管理  
   a 余额充足情况，可以交易，不存在负债   
   b 余额不充足情况，是否可借款做交易，存在负债   
   c 入账时，永远都是先处理负债   
8、爆仓计算期间取款操作，有保证金，另外附加取款延迟30分钟到账（处理取款时，正在计算爆仓）。


clearing主要是管理account各个科目的资金变动及仓位调整  
account提供科目更新服务  
position提供仓位更新服务  


  
ddd cut -> stateless -> serverless


SQS
1、FIFO queues support up to 300 messages per second (300 send, receive, or delete operations per second). When you batch 10 messages per operation (maximum), FIFO queues can support up to 3,000 messages per second. If you require higher throughput, you can enable high throughput mode for FIFO on the Amazon SQS console, which will support up to 30,000 messages per second with batching, or up to 3,000 messages per second without batching.  
2、Amazon SQS has a deduplication interval of 5 minutes.   
3、
