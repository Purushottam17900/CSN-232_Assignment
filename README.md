## The Readers – Writers Problem
Suppose a resource such as a database is to be shared among several concurrent processes. The processes may be of two types, viz. readers and writers. A reader only wants to read the contents of the database and not modify it, whereas a writer wants to modify the database. 

Now as the processes are concurrent, more than one process may want to access the database at a time. If the processes trying to access are all readers, then there will not be any problem. But if there is a writer and some other process (maybe a reader or a writer) accessing the database simultaneously, desired results are not obtained. This synchronization problem is referred to as the **readers-writers problem**.

Based on the priorities, the readers-writers problem may have different variations. The first one called the *first readers–writers problem* gives priority to the readers. It ensures that any reader may use the shared resource as long as there is no writer using the resource. *This may lead to starvation of the writer*. The second one called the *second readers–writers problem* gives priority to the writers. It ensures that a writer may perform its operation as soon as possible regardless of the readers waiting to use the shared resource. *This may lead to starvation of the reader*.

## Starve - Free Readers – Writers Problem

So, we see that a solution to either problem leads to starvation. In the first case the writers may starve while in the second case, the readers may starve. There is another variation to the problem which solves the starvation issues of the previous problems. It is called the **third readers-writers problem**. 

An approach to the starve-free readers-writers problem or the third readers-writers problem is enclosed alongwith. It encompasses the use of semaphores in the solution to problem. To ensure that there is no starvation of processes, the semaphores have to preserve the *FIFO* ordering when they allow the processes to request for the use of shared resource.
