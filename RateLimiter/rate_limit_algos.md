Rate limiter algorithms:-

# Token bucket
    Token is filled every X seconds as a fixed interval
    Bucket has a capacity C
    Each request reduces the tokens in the bucket by 1
    If the bucket is empty and doesnt have any token left, the subsequent requests are dropped.  

    Disadvantage:
    At the edge this can allow more number of requests as if all the requests are processed before and after bucket was refilled.

# Leaky bucket
    Requests are added in a queue (FIFO) and are processed at a constant fixed rate.

    It has max qLen, rate in, rate out confugurations

    Disadvantage:
    If the bucket is full due to burst of traffic, all new subsequent requests will suffer.

# Fixed window counter
    1. Time is divied in fixed interval windows
    2. Each window has it's counter
    3. Each incoming request that is processed, increases the counter by 1, once the counter reaches the limit new requests are discarded.

    Disadvantage:
    At the edge of the time window we can have more than allowed requests in a time duration

# Sliding window log
    Fixed window size is a rolling window like 1 min, 10sec etc
    The window is rolling and req time are added to the log
    The sliding window maintains the count in the given time window


    IF the number of requests exceed the number of req in the window the req is discarded
    As window moves ahead, entries are removed that are outside of the window still in the log

# Sliding window counter

    Requires less memory as per sliding window log

    We have different window intervals, in each interval we have the counter. We do not store each req ts in the log, rather we store the counter for each interval.