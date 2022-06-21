# ats
used to be a trade system, but now is aws trade system.

## Thinking
- should use serverless to provide query service worked with DB synchronization. By doing this, user can query info during maintenance.
- instead of pushing, could use pull mode to calculate position in RTC.
- the api return exactly what error is to frontend, i18n should be done with cdn resource file. enum type info should use common english expression.
- all data info don't contain language field, expression common info. i18n should be done in front end.
- decouple frontend api and backend api
- update config parameter notify by MQ, and then serverless update distributed cache
- serverless provider api in the front of distributed cache
- core logical don't need cache parameters, take associated parameters when place order. frequently need parameter = request with parameter
- tick messages work with container + MQ, trade notify message work with serverless + MQ
- real time message(MQ)、cpu-intensive compute service(hazelcast)、data stream handle(hazelcast)
- use serverless to schedule job
- expose standard security openapi/asyncapi with version in order to generate doc 、 test script(postman) and client example code
- domain-driven design, cut into some dependent modules. every module has database.
- efficient UI design tool
- archive history data to another store
- store market data by time series db or just simple file with s3 select
- final architecture should privide aws resource's price and benchmark info
- download could result in data leaking
- requirement and bug track management
- writing less code can make a powerful testsuit and wonderful document.
- important middleware should work with rotate password
- schedule backup data info and can be restored with any version of data backup.
- don't give root to application service.
- auto response to common application service fail
- config data can physically delete, user data just logically delete
- each administrator operations should be written down log
- trade info sequence generate(pure number or prefix + number)
- cpu-intensive function only need integer/long data to handle
- core service must guarantee cluster. invalid request should be blocked by waf or gateway
- every symbol's market data must be kept only one version.
- microservice means seperating infrastructure as well, not just service code.
- CUD should be in only one database, and then synchronize data to sharding database for R.
- cache service just provide specific key query with lur strategy, don't provide like function query or permanent store. if want to use key-value database, just choose sth like DynamoDB.
- nosql store config info
- request parameters must be checked strongly
- before production, list all points that should be monitored, every monitor has automatically handle as possible as we can.
- every resources(cpu、memory、disk、config data、user data) should have lifecycle.
- every domain services communication by event should be tolerated reboot. After that, data will not lose.
- checking web by google Lighthouse and pagespeed; webpagetest 
- every microservice communicate by service mesh(sidecar)
- trade as a service
- every captical flow should have trace(taizhang)
- something like risk management or captical flow should be done after main process completes. don't effect main process. main process should privide service level agreement(SLA).
- every microservice should have its own metric to be monitored. automatically reaction.
- bussiness man, product manaagement, test team and developers keep unify view about system by documents. everything about system must be written in documents.
- request just only contains biz parameters, other function parameters should be in header.
- according to biz scenario, find out hot data to cache.
- api protection(DDoS)
- if use own middleware, just install on the VM, not use container.
- cloudwatch + lambda + batch= job
- deploy version(story) and maven version(interface) management
- bug/requirement work by github issue.
- log level can be changed by pub/sub or http request.
- elasticbeanstalk can provide back end management system.
- low code work with back end management system.
- ios/android/web have Event Tracking.
- every biz client(web/app) request should have a idempotent-request-id in order to retry. idempotent-request-id can be stored in cache with expiration. expiration work with time, for example if a request-id will be expired in a day, then yesterday request-id should be blocked no matter what it is.
- every dependent middleware should be config by container evn, don't config by config file.
- MQ will have two kinds of message, orginal message and common biz message.
- seperate payment/withdraw module from core biz. dependently deploy payment/withdraw service.
- find exception or error as soon as possible. before client's awareness. by cloudwatch.
- do chaos as routine
- data backup frequently
- check middleware(by Common vulnerabilities and exposures)、web application(owasp top 10) and hardware's vulnerabilities as routine. 
- api modification notified by email
- api should version
- return error contain HTTP response status codes and simple english expression detail, don't use magic number error code. 
- backoffice should provide every error request info query.
- using bot to bring common task into the chat interface.
- backoffice by beanstalk
- Small releases are great. They are easy to test. They simplify code review efforts. It's much easier to confidently release and deploy small sets of changes.
- zero trust model security architecture(don't open 22 port(ssh))
- real time tick only provide when user click specific symbol, other symbols just privide tick every fixed time.
- every query has export function.
- if status expressed by enum, use simple expression in english in common. don't define enum. 
- every enum should base on english.
- low code/no code aws Amplify Studio
- android/ios devices testing by browserstack or aws device farm, don't need physical device. 
- if real time service need config parameters, they should just have init and stop, don't allow to modify.
- if something express depends on client or server, e.g. exchange rate 、 ask or bid 、timezone. need fix one side. every relative logic depends on this rule.
- if multiple parameters express one logic, should integrate those to group one meesage to send. don't allow unfinished setting status. event source.
- trade core system provide asynchronous api, the result can be gotten by either acitive http query or passive websocket notify.
- if internal request include some sensitive info, must use https.
- connection is the key in activity system. should use graph database to group them.
- realtime calculation service should not been stopped. rolling update.
- using aws s3 serverless to store and query(s3 select) ohlcv csv/parquet. s3 work with cloudfront(CDN) instead of 行情站
- s3 and cloudfront work with web project(vue etc.)
- cross position = HPC(high performance computing) = HPC follows a tightly coupled compute principle where parallel processes depend on each other. chain computing from beginning.
- isolated position = HTC(high-throughput computing) =  HTC follows a loosely coupled principle where smaller processes are run independent of each other. 
- something function like RTC should be simple and native, to avoid gc, it can use object pool with big heap.
- related configuration should be done in the same time(guiding style) or providing default value.
- every service should be been shutdown graceful.
- each service communication with one another should by sidecar.
- web logical app complication means that don't have standard api.
- Centralized Microservice Logging. Containers offer an easy and standardized way to handle logs because you can write them to stdout and stderr. 
- every stored data should have lifespan. e.g. data archive.
- move large objects out of an expensive relational database(RDS/DynamoDB) into an object store(S3). cost effectively.
- aws rds mysql can use event_scheduler to auto delete old data. DynamoDB can use TTL to auto delete old data.
- snapshot -> sync data -> delete old data during financial maintaining time everyday. esp. relational database.
- each microservice should have specific cpu and memory usage information.
- sensitive data should be transferred with TSL/SSL in internal.
- every microservice should integrate with Consumer Driven Contract in development.
- start computing time is more important than finish computing time in RTC. put market tick and symbol position info into a HPC unit.
- http get and then put/patch should use optimistic lock with etag.
- if some data in some microservices need be consistent, should have data redundancy in the final data store. e.g. captial flow and total with captial flow no.
- scaling out with throttling, don't blindly scale out. scaling out by real time peak request or peak scheduled time.
- backend service should use optimistic lock to update data. avoiding overwrite.
- every external requests work with timeout setting(connect timeout/read timeout).
- strong consistency in cats is unnecessary. eventual consistency is enough. substract user balance and then open a new position, close a position and then add user balance. deposit money and then add user balance, substrct balance and then withdraw money. use command message to notify clearing service.
- every microservice communicates with each other by protobuf.
- client ui have some place to store error/exception info. in order to give dev to solve the problem.
- If use retry, should issue retry in source, not middle service.
- wanfa(future/spot/margin) is about money management, trade mode is about logic of trade.
- between two busy servie should have a high available middleware, don't let one service to push another one directly.
- order/deal/position should be query by id or account with dynamodb. user info/system config work with rds.
- eventually consistence can't work with action that depends on condition query.
- every biz data that depends on config should have whole lifespan. When scenario with config to create, when not to create. 
- avoid designing upfront and change requirement frequently.
- continous refactoring with automation test in Agile.
- think out everything -> agile development -> manually testing
- upfront architecture limits development speed.
- mobile and desktop should have different application service to provide api. in order to test respectively.
- market data should have fixed rate to give data to other consumer. external data source should be limited or it can push lots of data to kernel.
- In market data hpc service, should shutdown log in production and privide realtime log in stage env. In trade hpc service, should shutdown log and privde the whole answer with expression/processing info to downstream.
- While CodeQL detects security issues in your own code, Dependabot detects vulnerabilities that originate from your project’s dependencies.
- cryptocurrency decimal problem.   
>>  long max: 9,223,372,036,854,775,807                19 decimal places(100million/10 decimal places)    
>>  btc:        20,999,999.99999999                     8 decimal places    
>>  usd:40,000,000,000,000.00                           2 decimal places  
>>  eth:        17,999,999.999999999999999999/per year 18 decimal places  
>>  future-usdt: coin/usdt  
>>  future-coin: coin/btc(usd)  
>>  notional value: limit quantity  
>>  max limit: limit price value  
>>1. avoid multiplication(price * quantity)  
>>2. if there is a big total price order, can use it divided by price to get quantity  
>>3. have min/max limit, min control asset decimal places, max control price.
>>4. cryptocurrency address support 18 decimal places, but arithematic operation only support 10 decimal places  
>>> e.g.  
>>> notinoal value:10.00000000  
>>> my asset: 0.000000000000000001  
>>> eth/usdt last price: 1/1,774.04  
>>> multipy: 0.000000000000000001 * 1,774.04 = 0.000000000177404 (15 decimal places)  
>>> divide: 10 / 1774.04 = 0.005636851480237199(15 decimal places)  
>>> min quantity limit: 0.0001  
>>> only check if my asset is great than 0.0001  
- captical flow = lock -> transfer -> unlock

