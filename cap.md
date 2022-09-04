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
16、按玩法分别创建queue，每个queue根据公司编号+账户编号创建message group id进行定序。 后续整个系统的停机策略按平台停、按公司停、按公司指定账户组停。每次producer和consumer启动时，获取QueueList，按照公司 + 玩法 + 客户组 + 账户编号四级维度依次匹配获取对应的发送或者接收queue。获取对应的Queue名称规则后，即可确认message group id规则。如果大维度的Queue满足不了性能时，需要暂停服务，重新创建小维度Queue，然后启动服务。         
    Queue名称：         公司_玩法_客户组 -> 公司_玩法      -> 公司   
    message group id：  账户编号       -> 客户组_账户编号 -> 玩法_客户组_账户编号  
    
17、使用ecs提供的Rolling update更新服务，保证不停机。   
18、计算所需热数据保存在主库，用户或者营运查询数据同步到只读库（可以单独创建不同主库的索引），用户或者营运汇总数据查询同步到mongodb。  
19、cloudwatch监控SQS实现自动ecs扩容服务。   
20、所有cap服务，需要实现shutdownhook。    

account  
1、存在跨两个白标公司账务处理？  
2、凭证登记无需记录数据库，凭证主要的作用是避免重启或者其他原因导致account不可用时，正常后可以继续处理，MQ天然支持。  
3、现在的开户是有账务处理的时候，才开？不应该是用户在界面上手动点击开启什么玩法？如果需要，开户也要跟账务处理逻辑分开  
4、流水id需要顺序的原因？  
5、流水账务持久化？只看到添加ignite逻辑。（write-through？）  
6、自动借款，负债还款，应该属于通用account逻辑的一部分，如果要区分，以请求参数的方式调用account。  
   abcc：借款额度、总授信、是否可借、全仓账户币种(usdt/hkd/usd)[信用卡借款，币种是跟开卡币种一致，再换汇成其他币种，还款也是还开卡币种]  
7、账务处理分两类，把一笔资金做逻辑划分到不同科目，便于账务分析和管理。根据公式分解一笔入账到指定科目或者从指定科目出账。   
   a 余额充足情况，可以交易，不存在负债   
   b 余额不充足情况，是否可借款做交易，存在负债   
   c 入账时，永远都是先处理负债   
8、爆仓计算期间取款操作，有保证金，另外附加取款延迟30分钟到账（处理取款时，正在计算爆仓）。   
9、当处理一个账户账务时，发生异常，直接修改账户状态，且返回失败，clearing消费者将此消息转移到DLQ中，后续如果继续有此账户的账务处理，account判断账户状态为异常后，继续返回失败，转DLQ。待后续正常后，重新进行消费。   


clearing主要是管理account各个科目的资金变动及仓位调整  
account提供科目更新服务  
position提供仓位更新服务  


  
ddd cut -> stateless -> serverless


SQS    
1、FIFO queues support up to 300 messages per second (300 send, receive, or delete operations per second). When you batch 10 messages per operation (maximum), FIFO queues can support up to 3,000 messages per second. If you require higher throughput, you can enable high throughput mode for FIFO on the Amazon SQS console, which will support up to 30,000 messages per second with batching, or up to 3,000 messages per second without batching.  
2、If a message with a particular message deduplication ID is sent successfully, any messages sent with the same message deduplication ID are accepted successfully but aren't delivered during the 5-minute deduplication interval.  
3、There is no quota to the number of message groups within a FIFO queue.  
4、You can't request to receive messages with a specific message group ID.  
5、It is possible to receive up to 10 messages in a single call using the MaxNumberOfMessages request parameter of the ReceiveMessage action.   
6、The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues.    
7、it is a best practice to always set the retention period of a dead-letter queue to be longer than the retention period of the original queue.    
8、if you view a message in the console the number of times specified in the corresponding queue's redrive policy, the message is moved to the corresponding queue's dead-letter queue.   
    Increase the Maximum Receives setting for the corresponding queue's redrive policy.  
    Avoid viewing the corresponding queue's messages in the console.   
9、A message is considered to be stored after it is sent to a queue by a producer, but not yet received from the queue by a consumer (that is, between states 1 and 2). There is no quota to the number of stored messages. A message is considered to be in flight after it is received from a queue by a consumer, but not yet deleted from the queue (that is, between states 2 and 3). There is a quota to the number of in flight messages.   
10、For FIFO queues, there can be a maximum of 20,000 in flight messages (received from a queue by a consumer, but not yet deleted from the queue).   
11、If you don't know how long it takes to process a message, create a heartbeat for your consumer process: Specify the initial visibility timeout (for example, 2 minutes) and then—as long as your consumer still works on the message—keep extending the visibility timeout by 2 minutes every minute.    
12、the new timeout period applies only to the particular receipt of the message. ChangeMessageVisibility doesn't affect the timeout of later receipts of the message or later queues.    
13、For FIFO queues, the per-queue delay setting is retroactive—changing the setting affects the delay of messages already in the queue.    
14、FIFO queues don't support timers on individual messages.   
15、During a long-lasting network outage that causes connectivity issues between your SDK and Amazon SQS, it's a best practice to provide the receive request attempt ID and to retry with the same receive request attempt ID if the SDK operation fails.   
16、don't recommend setting the number of maximum receives to 1 for a dead-letter queue.  
17、The Amazon SQS Buffered Asynchronous Client doesn't currently support FIFO queues.   
18、When messages that belong to a particular message group ID are invisible, no other consumer can process messages with the same message group ID.  
19、FIFO Exactly-Once Processing。  
20、 before you process each message, double check to be sure its VisibilityTimeout hasn’t expired.    
21、The result of sending each message is reported individually in the response. Because the batch request can result in a combination of successful and unsuccessful actions, you should check for batch errors even when the call returns an HTTP status code of 200.   
22、 you should make sure that your queue isn't publicly accessible (accessible by everyone in the world or by any authenticated AWS user).   
23、1,000 queues per ListQueues request.   

