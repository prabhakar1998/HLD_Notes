
# Proxy/load-balancer
    We will have a load-balancer/proxy server sitting at the front
        This proxy will route the req to one of the metadata server
            this helps in case of the failure of the metadata server, also we would have a set of proxy servers to make sure the proxy/load-balancer isn't a single point of failure

# Metadata service:-
    We will have a rounding/metadata service
        Metadata service knows how's the master of which partition of a topic
            Matadata service routes the data to the appropriate queue server 

# Queue backend servers:-
    These servers hold a bunch of queue partiotions, 
    These servers will act primary node for a group of queue partitions, then will also act as follower for the remaining queue paritions


# Message delivery and deletion

    1. Message is stored in the queue partition with some expiry conditions
    2. Once consumer reads a message, message is set invisible for a set duration and consumer is expected to process and ack the message in this time. Once consumer acks the message, the message is set for deletion as it gets successfully processed.

    In case of lack of Ack, the message is kept in the queue for the retry and for the durability guarantee.


# Perfomance tuning

    With the use of CACHE, the ability to write fast/read fast is acheived for the higher throughput, also with a WAL file it can work on slow HDD for the durability of the messages, the messages can be flushed from the cache to HDD with spinning disks as the data is written sequentially.