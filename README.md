# ats
used to a trade system, but now aws trade system.

## Thinking
- should use serverless to provide query service worked with DB synchronization. By doing this, user can query info during maintenance.
- instead of pushing, could use pull mode to calculate position in RTC.
- the api return exactly what error is to frontend, i18n should be done with cdn resource file.
- decouple frontend api and backend api
- update config parameter notify by MQ, and then serverless update distributed cache
- serverless provider api in the front of distributed cache
- core logical don't need cache parameters, take associated parameters when place order. frequently need parameter = request with parameter
- tick messages work with container + MQ, trade notify message work with serverless + MQ
- real time message(MQ)、cpu-intensive compute service(hazelcast)、data stream handle(hazelcast)
- use serverless to schedule job
- expose standard security openapi/asyncapi(owasp top 10) in order to generate doc 、 test script(postman) and client example code
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
