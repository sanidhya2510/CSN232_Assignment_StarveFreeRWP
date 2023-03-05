# CSN232_Assignment_StarveFreeRWP
Assignment Submission, CSN232-Operating Systems, Pseudo Code Implementation of Starve Free Readers-Writers Problem

The following pseudocode outlines the implementation of readers and writers process in the starve free readers writer problem. The problem involves multiple readers and writers accessing a shared resource, and the goal is to ensure that they do not starve. The starve-free execution of the readers-writers problem is a synchronization solution that ensures all processes have an opportunity to access the shared resource without being blocked indefinitely. In a starve-free implementation, both readers and writers are granted access to the resource in a fair and balanced manner, without any process being consistently favored over others.

## Global Variables and Semaphore

```
Semaphore Read_In; 
Semaphore Read_Out;
Semaphore Write_Now;

int count_started_reading; 
int count_finished_reading;

boolean writer_on_wait;
```

count_started_reading: a variable that keeps track of the number of readers that have started reading from the shared resource.
count_finished_reading: a variable that keeps track of the number of readers that have finished reading from the shared resource.
writer_on_wait: a boolean flag that is set to true if a writer is waiting to write to the shared resource.
Read_In: a semaphore that controls access to the critical section by readers and writers. Readers and writers must acquire this semaphore before accessing the critical section.
Read_Out: a semaphore that controls access to the exit section by readers and writers. Readers and writers must acquire this semaphore before exiting the critical section.
Write_Now: a semaphore that is signaled by readers when they have finished reading and there are writers waiting to write to the shared resource. When signaled, the writer process can proceed to write to the shared resource.


## Initialization of semaphores and other global variables

```
Read_In = 1;
Read_Out = 1;
Write_Now = 1;
count_started_reading = 0;
count_finished_reading = 0;
writer_on_wait = false;
```


## Pseduocode Implementation for the Readers Process

The following pseudocode outlines the implementation of the reader process in the starve free readers writer problem. The problem involves multiple readers and writers accessing a shared resource, and the goal is to ensure that readers do not starve, meaning that they should be able to access the resource even if there are writers waiting to write to the resource.

Entry Section:

The entry section is the beginning of the reader process and is executed before the critical section.
The reader process requests permission to enter the critical section by attempting to acquire the Read_In semaphore.
Once acquired, the count_started_reading variable is incremented, indicating that a reader has started reading.
The Read_In semaphore is then released, allowing other readers to enter.

Critical Section:

The critical section is the part of the code where the reader can read data from the shared resource.
This section should be designed such that only one reader can access the resource at a time.
This section is not explicitly defined in the pseudocode but should be included in the implementation.

Exit Section:

The exit section is executed after the reader has finished reading from the shared resource.
The reader process requests permission to enter the exit section by attempting to acquire the Read_Out semaphore.
Once acquired, the count_finished_reading variable is incremented, indicating that a reader has finished reading.
If there are writers waiting to write and all readers have finished reading, the writer is signaled to write to the shared resource using the Write_Now semaphore.
The Read_Out semaphore is then released, allowing other readers to exit.

Remainder Section:

The remainder section is the code that is executed after the exit section.
In this implementation, the reader process enters an infinite loop, indicating that it will continue to read from the shared resource until it is interrupted.


```
do{

    //Entry section

    wait(Read_In);
    count_started_reading++;
    signal(Read_In);

    /*
    Critical section
    Reader can read the data.
    */

    //Exit Section
    wait(Read_Out);
    count_finished_reading++;
   
    if(writer_on_wait && (count_started_reading == count_finished_reading)){
        signal(Write_Now);
    }

    signal(Read_Out);

    //Remainder Section

}while(1);
```

## Pseudocode implementation for the Writers Process

Entry Section:

The entry section is the beginning of the writer process and is executed before the critical section.
The writer process requests permission to enter the critical section by attempting to acquire both the Read_In and Read_Out semaphores.
If all readers have finished reading, the writer can proceed to write to the shared resource by signaling the Read_Out semaphore.
If readers are still reading, the writer sets the writer_on_wait flag to true and signals the Read_Out semaphore to allow readers to continue reading.
The writer then waits for the Write_Now semaphore to be signaled before proceeding to write to the shared resource.
Once the semaphore is signaled, the writer_on_wait flag is set to false, indicating that there are no writers waiting.

Critical Section:

The critical section is the part of the code where the writer can write data to the shared resource.
This section should be designed such that only one writer can access the resource at a time.
This section is not explicitly defined in the pseudocode but should be included in the implementation.

Exit Section:

The exit section is executed after the writer has finished writing to the shared resource.
The writer process releases the Read_In semaphore, allowing other writers and readers to access the shared resource.

Remainder Section:

The remainder section is the code that is executed after the exit section.
In this implementation, the writer process enters an infinite loop, indicating that it will continue to write to the shared resource until it is interrupted.



```
do{

    //Entry Section

    wait(Read_In);
    wait(Read_Out);

    if(count_started_reading == count_finished_reading){
        signal(Read_Out);
    }

    else{
        writer_on_wait = true;
        signal(Read_Out);
        wait(Write_Now);
        writer_on_wait = false;
    }

    /*
    Critical section
    Writer can write the data.
    */

    //Exit Section
    signal(Read_In);

    //Remainder Section

}while(1);
```

## Explanation of the solution

Consider the example 'RRRWRW'

Reader 1 enters the critical section, waits for the Read_Out semaphore, increments count_started_reading and signals the Read_In semaphore. Since no writer is waiting, Reader 1 can access the critical section immediately.

Reader 2 enters the critical section, waits for the Read_Out semaphore, increments count_started_reading and signals the Read_In semaphore. Since no writer is waiting, Reader 2 can access the critical section immediately.

Reader 3 enters the critical section, waits for the Read_Out semaphore, increments count_started_reading and signals the Read_In semaphore. Since no writer is waiting, Reader 3 can access the critical section immediately.

Writer 1 enters the critical section, waits for both the Read_In and Read_Out semaphores, and finds that count_started_reading is not equal to count_finished_reading.

The writer sets the writer_on_wait flag to true, signals the Read_Out semaphore and waits for the Write_Now semaphore to be signaled.

Reader 4 enters the critical section, waits for the Read_Out semaphore, increments count_started_reading and signals the Read_In semaphore. Since a writer is waiting, Reader 4 cannot access the critical section immediately and has to wait for the writer to finish writing.

Writer 1 finishes writing, signals the Write_Now semaphore, sets writer_on_wait to false, and releases the Read_Out semaphore. Since Reader 4 is waiting on the Read_Out semaphore, Reader 4 can now access the critical section.

Reader 4 enters the critical section, waits for the Read_Out semaphore, increments count_started_reading and signals the Read_In semaphore. Since no writer is waiting, Reader 4 can access the critical section immediately.

Writer 2 enters the critical section, waits for both the Read_In and Read_Out semaphores, and finds that count_started_reading is equal to count_finished_reading. The writer signals the Read_Out semaphore to allow readers to continue reading, and proceeds to write to the shared resource.

Writer 2 finishes writing, releases the Read_In semaphore, and exits the critical section.

The solution ensures that writers are not starved of the resource by allowing them to wait until all readers who were reading when the writer arrived have finished before allowing the writer to write. The use of semaphores to coordinate access to the shared resource ensures that readers and writers do not interfere with each other's operations, and allows the program to execute correctly and efficiently.
