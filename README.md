# Multithreaded_banking_application
This Banking Application is a Multithreaded synchronized application.Here,we are not using system defined mutex locks instead created user defined mutex locks.



PROBLEM STATEMENT:
 
Consider a multithreaded banking application. The main process receives requests to tranfer money from one  account to the other, and each request is handled by a separate worker thread  in the application. All threads access shared data of all user bank counts. Bank accounts are represented by a unique integer account number, a balance, and a lock of type mylock (much like a pthreadsmutex) as shown below. 
struct account { intaccountnum; int balance; mylock lock; 
}; 

Each thread that receives a transfer request must implement the transfer function shown below, which transfers money from one account to the other. Add correct locking (by calling the dolock(&lock) and unlock(&lock) functions on a mylock variable) to the tranferfunc- tion below, so that no race conditions occur when several worker threads concurrently perform transfers. Note that you must use the fine-grained per account lock provided as part of the account object itself, and not a global lock of your own. Also make sure your solution is deadlock free, when multiple threads access the same pair of accounts concurrently. 
void transfer(struct account *from, struct account *to, int amount) { 
from->balance -= amount; // dont write anything... to->balance += amount; // ...between these two lines 
} 

INTRODUCTION: 

Processes are used to group resources together and threads are the entities scheduled for execution on the CPU. A thread is a single sequence stream within in a process. ... This is why thread needs its own stack. An operating system that has thread facility, the basic unit of CPU utilization is a thread.A  thread is a path of execution within a process. A process can contain multiple threads. The primary difference is that threads within the same process run in a shared memory space, while processes run in separate memory spaces. 
Thread synchronization is defined as a mechanism which ensures that two or more concurrent processes or threads do not simultaneously execute some particular program segment known as critical section. Processes’ access to critical section is controlled by using synchronization techniques. When one thread starts executing the critical section (serialized segment of the program) the other thread should wait until the first thread finishes. If proper synchronization techniques are not applied, it may cause a race condition where the values of variables may be unpredictable and vary depending on the timings of context switches of the processes or threads. The most popular way of achieving thread synchronization is by using mutexes.A Mutex is a lock that we set before using a shared resource and release after using it.When the lock is set, no other thread can access the locked region of code.So we see that even if thread 2 is scheduled while thread 1 was not done accessing the shared resource and the code is locked by thread 1 using mutexes then thread 2 cannot even access that region of code.So this ensures a synchronized access of shared resources in the code. 

Working of a mutex 
Suppose one thread has locked a region of code using mutex and is executing that piece of code. Now if scheduler decides to do a context switch, then all the other threads which are ready to execute the same region are unblocked. Only one of all the threads would make it to the execution but if this thread tries to execute the same region of code that is already locked then it will again go to sleep. Context switch will take place again and again but no thread would be able to execute the locked region of code until the mutex lock over it is released. Mutex lock will only be released by the thread who locked it. So this ensures that once a thread has locked a piece of code then no other thread can execute the same region until it is unlocked by the thread who locked it. 
A mutex is initialized and then a lock is achieved by calling the following two functions : The first function initializes a mutex and through second function any critical region in the code can be locked. 1. int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr) : Creates a mutex, referenced by mutex, with attributes specified by attr. If attr is NULL, the default mutex attribute (NONRECURSIVE) is used. Returned value If successful, pthread_mutex_init() returns 0, and the state of the mutex becomes initialized and unlocked. If unsuccessful, pthread_mutex_init() returns -1. 2. int pthread_mutex_lock(pthread_mutex_t *mutex) : Locks a mutex object, which identifies a mutex. If the mutex is already locked by another thread, the thread 

 
waits for the mutex to become available. The thread that has locked a mutex becomes its current owner and remains the owner until the same thread has unlocked it.When the mutex has the attribute of recursive, the use of the lock may be different. When this kind of mutex is locked multiple times by the same thread, then a count is incremented and no waiting thread is posted. The owning thread must call pthread_mutex_unlock() the same number of times to decrement the count to zero. Returned value If successful, pthread_mutex_lock() returns 0. If unsuccessful, pthread_mutex_lock() returns -1. The mutex can be unlocked and destroyed by calling following two functions :The first function releases the lock and the second function destroys the lock so that it cannot be used anywhere in future. 3. int pthread_mutex_unlock(pthread_mutex_t *mutex) : Releases a mutex object. If one or more threads are waiting to lock the mutex, pthread_mutex_unlock() causes one of those threads to return from pthread_mutex_lock() with the mutex object acquired. If no threads are waiting for the mutex, the mutex unlocks with no current owner.When the mutex has the attribute of recursive the use of the lock may be different. When this kind of mutex is locked multiple times by the same thread, then unlock will decrement the count and no waiting thread is posted to continue running with the lock. If the count is decremented to zero, then the mutex is released and if any thread is waiting it is posted. Returned value If successful, pthread_mutex_unlock() returns 0. If unsuccessful, pthread_mutex_unlock() returns -1 4. int pthread_mutex_destroy(pthread_mutex_t *mutex) : Deletes a mutex object, which identifies a mutex. Mutexes are used to protect shared resources. mutex is set to an invalid value, but can be reinitialized using pthread_mutex_init(). Returned value If successful, pthread_mutex_destroy() returns 0. If unsuccessful, pthread_mutex_destroy() returns -1. 
 
 
 
 IMPLEMENTATION: 
 In this program,we are using pthreads to implement the banking account in which the number of users are equal to the number of threads in the program which are carrying out different processes. Every transaction is carried out using threads and shared data of user.Instead of using mutex locks which are predefied in pthread library,we have defined our own lock and unlock functions by the name dolock() and unlock().We have stored the information about user consisting of bank account number and balance in a structure s. 
 We have stored information of two users with account number 1 and 2 and all the other new account numbers will start from account number 3. 
 UNLOCK() 
 We have initialised a global variable unlock=0.So when a process enters,if value of unlock=0,then it enters critical section and unlock is incremented,else it will loop till unlock=0. 
 DOLOCK() 
  After the process exits critical section,dolock changes the value of unlock to 0. 
 We have initialised thread in main.Whenever we call a transfer using MakeTransaction(),withdraw() or deposit(), the program will ask how many users want to use the service and number of threads equal to the number of users will be created and the appropriate function will be called. 
 WITHDRAW() 
 When this function is called from the main() the very first user thread will lock the critical section and only after the user has withdrawn,the critical section will be unlocked and only after that other user threads can enter critical section.The same procedure will take place for all users. 
 DEPOSIT() 
 When this function is called from the main() the very first user thread will lock the critical section and only after the user has deposited,the critical section will be unlocked and only after that other user threads can enter critical section.The same procedure will take place for all users. 
 MAKETRANSACTION() 
 When this function is called from the main() the function will ask for two account numbers:1.from which account we need to make transaction and 2.to which account the transaction is being made.After this the user thread will lock the critical section and only after the user has successfully completed the transaction,the critical section will be unlocked and only after that other user threads can enter critical section.The same procedure will take place for all users.  
 



