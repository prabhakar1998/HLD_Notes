High level design:

Rate-limitter will be a seperate service:
    1. Rule based rate limiting

        domain: Name
        descriptors:
            -key: message_type
            value: marketing
        rate_limit:
            unit: day
            requests_per_unit: 5


Client hits web server / API server:
    API server hits rate-limitter service:
        Rate limitter reverts back with allowed/rejected:
            Webserver processes the request/ throtelled the request
        

User requests will have unique identifiers to understand whether to limit the request or not

    1. Rules are fetched from the rules cache/rule db
    2. rate limitter algo will check if the request has to be processed or dropped
    3. Either the request is rejected with 429 status code for too many requests, or request is send to the queue to be processed later


What to do if rate-limitter fails?
    1. System should continue to accept the requests and let the system stay operational.
    2. Rate limitter should be fixed promptly by the engineering team as it's one of the critical component.

Race conditions (High concurrency handling):
    If two processes are getting the counter, incrementing the counter and then setting the value back up.
    If the increments are not atomic in nature, then this can occur where rate limitter 

After request is valid, the rate limiter should allow the request first and then should update the cache with the new counter or incremented counter value. The counter shouldn't be updated before forwaring the request for processing.
This boosts the performance by not blocking the client.





Advantages of this design:

    1. Avalaibility:
        Since the rate limiter is running as a seperate service, even if one of the pod goes down other rate limiter pods wil handle the load.
    2. Low latency:
        The metadata/counter is fetched from the cache first and then from the DB in case of cache miss (faster reads).
        Client request is first forwarded for processing before the DB/cache is updated.
    3. Scalablity:
        Ratelimiter servers can be increased/decreased based on the number of requests incoming.