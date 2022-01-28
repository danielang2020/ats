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
- expose standard security openapi/asyncapi in order to generate doc 、 test script(postman) and client example code
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
- every resources(cpu、memory、disk、config data、user data) should have lifespan.
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
- using aws s3 serverless to store and query ohlcv csv.
- cross position = HPC(high performance computing) = HPC follows a tightly coupled compute principle where parallel processes depend on each other. chain computing from beginning.
- isolated position = HTC(high-throughput computing) =  HTC follows a loosely coupled principle where smaller processes are run independent of each other. 
- something function like RTC should be simple and native, to avoid gc, it can use object pool with big heap.
- related configuration should be done in the same time(guiding style) or providing default value.
- every service should be been shutdown graceful.
- each service communication with one another should by sidecar.
- web logical app complication means that don't have standard api.
