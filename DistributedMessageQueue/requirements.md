Requirements:-
    Functional requirements:

        1. Queue createion:
            Clients should be able to create queue with parameters like name, maxMessageSize, retention etc
        2. Send message
            Producers be able to send the message
        3. Receive message  
            Consumers be abel to receive the message
        4. Delete queue
            Be able to delete the queue


    Non-Functional requirements:-
        Durability: Messsages should be durable, shouldn't be lost, if consumers are failing queue should withhold the message without loss
        Avalaibility: Queue should be highly availaible and fault tolerant 
        Performance: Should offer low latency for reading and writing messages
        Scalability: Queue should be scalable on demand as the load increases on the system

    

    Building blocks for the message queue:-
        1. Database for storing the metadata
        2. Cache for storing the recent messages, that can be read with low latency. Writing messages to the cache in low latency
        3. Load balancer/proxy this will redirect the client connection to the appropriate queue