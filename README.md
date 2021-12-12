# ats
used to a trade system, but now aws trade system.

## Thinking
- should use serverless to provide query service worked with DB synchronization. By doing this, user can query info during maintenance.
- instead of pushing, could use pull mode to calculate position in RTC.
- the api return exactly what error is to frontend, i18n should be done with cdn resource file. enum type info should use common english expression.
- decouple frontend api and backend api
- update config parameter notify by MQ, and then serverless update distributed cache
- serverless provider api in the front of distributed cache
- core logical don't need cache parameters, take associated parameters when place order. frequently need parameter = request with parameter
- tick messages work with container + MQ, trade notify message work with serverless + MQ
- real time message(MQ)、cpu-intensive compute service(hazelcast)、data stream handle(hazelcast)
- use serverless to schedule job
- expose standard security openapi/asyncapi in order to generate doc 、 test script(postman) and client example code
- domain-driven design, cut into some dependent modules.
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
- cache service just provide specific key query with lur strategy, don't provide like query or permanent store.
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
- every biz client(web/app) request should have a idempotent-request-id in order to retry. idempotent-request-id can be stored in cache with expiration.
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
- data handle by jpa
- backoffice by beanstalk
