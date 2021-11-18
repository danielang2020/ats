# ats
used to a trade system, but now aws trade system.

## Thinking
- should use serverless to provide query service worked with DB synchronization. By doing this, user can query info during maintenance.
- instead of pushing, could use pull mode to calculate position in RTC.
- the api return exactly what error is to frontend, i18n should be done with cdn resource file.
- decouple frontend api and backend api
- update config parameter notify by MQ, and then serverless update distributed cache
