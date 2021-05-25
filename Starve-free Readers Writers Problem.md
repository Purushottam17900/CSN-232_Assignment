
```cpp
struct process{
    int *value;
    process *next;                  //address of the next block              
}

struct FIFOqueue{
    process *head, *tail;

    /*Function to add a new process to the back of the queue*/
    void push(int *x){
        process *newprocess = new process();
        newprocess->value = x;
        if(tail == NULL){
            head = newprocess;
            tail = newprocess;
        }
        else{
            tail.next = newprocess;
            tail = newprocess;
        }
    } 

    /*Function to remove the first process from the queue*/
    int* pop(){
        if(head == NULL){
            return -1;
        }
        int *x = head->value;
        if(head == tail){
            head = NULL;
            tail = NULL;
        }
        else{
            head = head->next;
        }
        return x;
    }
}

struct semaphore{
    int value = 1;                  //semaphore value initialised to 1
    FIFOqueue *queue = new FIFOqueue();
}

void wait(semaphore *s, int *pid){
    s->value = s->value - 1;        //semaphore value decremented
    if(s->value < 0){
        s->queue.push(pid);
        sleepProcess(pid);          //Using a function call, the process sleeps
    }
}

void signal(semaphore *s){
    s->value = s->value + 1;        //semaphore value incremented
    if(s->value <= 0){
        int *pid = s->queue.pop();
        wakeProcess(pid);           //Using a function call, the process is awakened
    }
}

```
## VARIABLES

We declare the following variables which will be used in the reader's and writer's code.

```cpp
int read_count = 0;                 //number of readers accessing the resource currently
semaphore rw_mutex;                 //semaphore to control the access of readers and writers to the resource
semaphore mutex,                    //semaphore to control the access of the variable read_count
semaphore request_order;            //semaphore to preserve the ordering of requests to handle starvation
```


## READER PROCESS CODE
```cpp
reader(){
        <ENTRY SECTION>
        wait(request_order);            //The reader waits for its turn to access the resource
        wait(mutex);                    //The reader requests for permission to access the variable read_count
        read_count++;                   //As the reader is going to access the resource, the variable read_count is incremented
        if(read_count == 1){            //If the variable read_count is 1, i.e. the current reader is the first reader to access the resource,
            wait(rw_mutex);             //  the reader requests for the access to the resource on behalf of all the readers, thus effectively  
        }                               //  blocking the resource for the writers
        signal(request_order);          //The next processes in line are now free to request for the access to the resource
        signal(mutex);                  //The read_count variable is freed to be used by other readers

        <CRITICAL SECTION>              //Reading performed by the reader

        <EXIT SECTION>
        wait(mutex);                    //Now the reader which wants to leave the resource requests for permission to access the variable read_count
        read_count--;                   //The variable read_count is decremented
        if(read_count == 0){            //If the variable read_count is 0, i.e. the current reader is the last reader to leave the resource,                
            signal(rw_mutex);           //  the writers are now free to access the resource                
        }
        signal(mutex);                  //The read_count variable is freed to be used by other readers
}
```

## WRITER PROCESS CODE
```cpp
writer(){
        <ENTRY SECTION>
        wait(request_order);            //The writer waits for its turn to access the resource
        wait(rw_mutex);                 //The resource is now to be engaged by a writer so the other processes(readers+writers) are blocked to use
        signal(request_order);          //The next processes in line are now free to request for the access to the resource

        <CRITICAL SECTION>              //Writing performed by the writer

        <EXIT SECTION>
        signal(rw_mutex);               //It releases the resource for the use of next process(reader/writer)
}
```
