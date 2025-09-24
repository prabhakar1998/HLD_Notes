1. Ordering of the messages?

    Ordering and concurrent access to read/write to the queue

        a. Best effort ordering
        b. Strict ordering

    
    Best effort ordering (order by time of receiving the message):-
        Order in which messages are received and not in the order of production of the message

    Strict ordering (order by produced time):-   
        Messages are placed in the queue in the order in which messages are produced.
    
        Based on some unique identifier or the timestamp


    Ordering will require sorted in the realtime as online sorting algo, where items are arriving as they get sorted.
    To make sure that queue is accessed concurrently without locking, we can use a buffered storage for the same, the messages are added to this buffered storage and then are read or delivered from it.