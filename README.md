# CSN232_Assignment_StarveFreeRWP
Assignment Submission, CSN232-Operating Systems, Pseudo Code Implementation of Starve Free Readers-Writers Problem

# Global Variables and Semaphore

```
Semaphore Read_In; //A semaphore accquired by the Reader Process to increment the count_started_reading variable
Semaphore Read_Out; //A semaphore accquired by the Reader Process to increment the count_finished_reading variable
Semaphore Write_Now; //A semaphore accquired by the Writer Process when it wants to execute in the critical section

int count_started_reading; //A counter which counts how many readers have started reading
int count_finished_reading; //A counter which counts how many readers have completed reading

boolean writer_on_wait; //A boolean which tells whether the writer is waiting to write or not
```



# Initialization of semaphores and other global variables

```
Read_In = 1;
Read_Out = 1;
Write_Now = 1;
count_started_reading = 0;
count_finished_reading = 0;
writer_on_wait = false;
```


# Pseduocode Implementation for the Readers Process

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

# Pseudocode implementation for the Writers Process

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
