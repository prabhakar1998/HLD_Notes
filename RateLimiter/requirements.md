Why ?
    1. Preventing resource starvation: DDOS attacks
    2. Managing policies and quotas
    3. Avoiding excess costs: Limiting experiments


Requirements:-
    
    Functional:
        1. User should be able to set threshold for requests allowed
        2. Response with error as 429 (Too many requests)

    Non-functional:
        1. Low latency shouldnt bottleneck the performance (quick and fast)
        2. Highly avalaible as all the services will rely on rate limitter
        3. scalable: as the actual local or scale of the system will depend on this as well

Types of Throttling__:
    1. Hard throttling:
        Fixed, no extra requests will be processed
    2. Soft throttling:
        X % of requests addditionally can be processed
    3. Dynamic throttling or Elastic:
        If the system has avalaible resources then the system will adapt and dynamically scale based on the needs.


Where to place the rate limitter:
    1. At the client side:- At the UI you can limit number of requests that the system sends (this isn't safe but can be done, can get manipulated)
    2. On the server side:, at the application layer, the application decides if the request has to be processed
    3. Middleware: Stays b/w client and server



Methods to implement the rate limitter:
    1. A ratelimiter with centralised database
        Redis/Postgres is used by all the services in place
    2. A ratelimiter with distributed database
        Sticky sessions, each node has it's db to check the rate limits


Building blocs:
    1. DB to store settings/metadata/counters
    2. Cache to store counters/metadata
    3. Holding the incoming requests

