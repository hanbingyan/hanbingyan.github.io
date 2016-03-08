---
published: true
title: PThreads学习
category: linux
tags: 
  - cpp
  - c
layout: post
---


*TOC
{:TOC}

<!--more-->

# 前言

本文档参考: <https://computing.llnl.gov/tutorials/pthreads/>

Pthreads 是 IEEE（电子和电气工程师协会）委员会开发的一组线程接口，负责指定便携式操作系统接口（POSIX）。Pthreads 中的 P 表示 POSIX，实际上，Pthreads 有时候也代表 POSIX 线程。基本上，POSIX 委员会定义了一系列基本功能和数据结构，它希望能够被大量厂商采用，因此线程代码能够轻松地在操作系统上移植。委员会的梦想由 UNIX 厂商实现了，他们都大规模实施 Pthreads。（最著名的例外就是 Sun，它继续采用 Solaris* 线程作为其主要线程 API。）由于 Linux 的采用和移植到 Windows 平台，Pthreads 的可能性一直被进一步扩展。

Pthreads 指定 API 来处理线程要求的大部分行为。这些行为包括创建和终止线程、等待线程完成、以及管理线程之间的交互。后面的目录中存在各种锁定机制，能够阻止两个线程同时尝试修改相同的数据值：互斥锁、条件变量和信号量。（从技术上讲，信号量不是 Pthreads 的一部分，但是它们从概念上更接近于与线程合作，而且可用于 Pthreads 能够运行的所有系统上。）

在Linux环境下，可以在Shell中通过man 查询到PThreads的每个函数命令,如： man pthread_create



# 线程的创建和管理

每个线程都有一个在进程中唯一的线程标识符，用一个数据类型 pthread_t 表示，该数据类型在Linux中就是一个无符号长整型数据。

## 创建新的线程

```cpp
int pthread_create (pthread_t *thread,pthread_attr_t *attr,void *(*start_routine)(void *),void *arg)
```
若创建成功，返回0；若出错，则返回错误编号. thread是线程标识符，但这个参数不是由由用户指定的，而是由pthread_create函数在创建时将新的线程的标识符放到这个变量中. attr指定线程的属性，可以用NULL表示默认属性. start_routine指定线程开始运行的函数，arg是start_routine所需要的参数，是一个无类型指针.
默认地，线程在被创建时要被赋予一定的属性，这个属性存放在数据类型pthread_attr_t中，它包含了线程的调度策略，堆栈的相关信息，join or detach 的状态等。
pthread_attr_init 和pthread_attr_destroy 函数分别用来创建和销毁pthread_attr_t，具体函数申明可参考man帮助. 



##结束线程

当发生以下情形之一时，线程就会结束：

线程运行的函数return了，也就是线程的任务已经完成；

线程调用了pthread_exit函数；

其他线程调用pthread_cancel 结束这个线程；

进程调用 exec() or exit()，结束了；

main() 函数先结束了，而且自己没有调用pthread_exit来等所有线程完成任务。

当然，一个线程结束，并不意味着它的所有信息都已经消失，后面会看到僵尸线程的问题。

下面介绍这两个函数。

```cpp
void pthread_exit (void *retval)
```
retval是由用户指定的参数，pthread_exit完成之后可以通过这个参数获得线程的退出状态

```cpp
int pthread_cancel (pthread_t thread)
```

一个线程可以通过调用pthread_cancel函数来请求取消同一进程中的线程，这个线程由thread参数指定。如果操作成功则返回0，失败则返回对应的错误编号.

看一个例子：

```cpp
/******************************************************************************
* FILE: hello.c
* DESCRIPTION:
* A "hello world" Pthreads program.  Demonstrates thread creation and termination.
* AUTHOR: Blaise Barney
* LAST REVISED: 08/09/11
******************************************************************************/
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#define NUM_THREADS	5

void *PrintHello(void *threadid)
{
   long tid;
   tid = (long)threadid;
   printf("Hello World! It's me, thread #%ld!\n", tid);
   pthread_exit(NULL);
}

int main(int argc, char *argv[])
{
   pthread_t threads[NUM_THREADS];
   int rc;
   long t;
   for(t=0;t<NUM_THREADS;t++){
     printf("In main: creating thread %ld\n", t);
     rc = pthread_create(&threads[t], NULL, PrintHello, (void *)t);
     if (rc){
       printf("ERROR; return code from pthread_create() is %d\n", rc);
       exit(-1);
       }
     }
   /* Last thing that main() should do */
   pthread_exit(NULL);
}
```
在Shell中输入以下命令编译执行

```cpp
gcc -Wall hello.c -lpthread -o hello
./hello
```
输出结果

```cpp
In main: creating thread 0
In main: creating thread 1
In main: creating thread 2
Hello World! It's me, thread #0!
In main: creating thread 3
In main: creating thread 4
Hello World! It's me, thread #3!
Hello World! It's me, thread #1!
Hello World! It's me, thread #4!
Hello World! It's me, thread #2!
```

下面这个例子传给线程一些初始化用的参数。

```cpp
/******************************************************************************
* FILE: hello_arg2.c
* DESCRIPTION:
*   A "hello world" Pthreads program which demonstrates another safe way
*   to pass arguments to threads during thread creation.  In this case,
*   a structure is used to pass multiple arguments.
* AUTHOR: Blaise Barney
* LAST REVISED: 01/29/09
******************************************************************************/
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#define NUM_THREADS	8

char *messages[NUM_THREADS];

struct thread_data
{
   int	thread_id;
   int  sum;
   char *message;
};

struct thread_data thread_data_array[NUM_THREADS];

void *PrintHello(void *threadarg)
{
   int taskid, sum;
   char *hello_msg;
   struct thread_data *my_data;

   sleep(1);
   my_data = (struct thread_data *) threadarg;
   taskid = my_data->thread_id;
   sum = my_data->sum;
   hello_msg = my_data->message;
   printf("Thread %d: %s  Sum=%d\n", taskid, hello_msg, sum);
   pthread_exit(NULL);
}

int main(int argc, char *argv[])
{
pthread_t threads[NUM_THREADS];
int *taskids[NUM_THREADS];
int rc, t, sum;

sum=0;
messages[0] = "English: Hello World!";
messages[1] = "French: Bonjour, le monde!";
messages[2] = "Spanish: Hola al mundo";
messages[3] = "Klingon: Nuq neH!";
messages[4] = "German: Guten Tag, Welt!"; 
messages[5] = "Russian: Zdravstvytye, mir!";
messages[6] = "Japan: Sekai e konnichiwa!";
messages[7] = "Latin: Orbis, te saluto!";

for(t=0;t<NUM_THREADS;t++) {
  sum = sum + t;
  thread_data_array[t].thread_id = t;
  thread_data_array[t].sum = sum;
  thread_data_array[t].message = messages[t];
  printf("Creating thread %d\n", t);
  rc = pthread_create(&threads[t], NULL, PrintHello, (void *) 
       &thread_data_array[t]);
  if (rc) {
    printf("ERROR; return code from pthread_create() is %d\n", rc);
    exit(-1);
    }
  }
pthread_exit(NULL);
}
```
输出结果：

```cpp
Creating thread 0
Creating thread 1
Creating thread 2
Creating thread 3
Creating thread 4
Creating thread 5
Creating thread 6
Creating thread 7
Thread 0: English: Hello World!  Sum=0
Thread 1: French: Bonjour, le monde!  Sum=1
Thread 2: Spanish: Hola al mundo  Sum=3
Thread 3: Klingon: Nuq neH!  Sum=6
Thread 4: German: Guten Tag, Welt!  Sum=10
Thread 5: Russian: Zdravstvytye, mir!  Sum=15
Thread 7: Latin: Orbis, te saluto!  Sum=28
Thread 6: Japan: Sekai e konnichiwa!  Sum=21
```
## 对线程的阻塞（Joining and Detaching Threads）

阻塞是线程之间同步的一种方法.

```cpp
int pthread_join(pthread_t threadid, void **value_ptr)
```
pthread_join 函数会让调用它的线程等待threadid线程运行结束之后再运行. value_ptr 存放了其他线程的返回值。一个可以被join的线程，仅仅可以被别的一个线程join，同时有多个线程尝试join同一个线程时，最终结果是未知的。另外，线程不能join自己。

上面提到过，创建一个线程时，要赋予它一定的属性，这其中就包括joinable or detachable 的属性，只有被声明成joinable的线程，可以被其他线程join.
POSIX标准的最终版本指出线程应该被设置成joinable的.显式地设置一个线程为joinable,需要以下四个步骤：

Declare a pthread attribute variable of the pthread_attr_t data type

Initialize the attribute variable with pthread_attr_init()

Set the attribute detached status with pthread_attr_setdetachstate()

When done, free library resources used by the attribute with pthread_attr_destroy()

值得注意的的是：僵尸线程（ "zombie" thread）是一种已经退出了的joinable的线程，但是等待其他线程调用pthread_join来join它，以收集它的退出信息(exit status). 如果没有调用pthread_join的话，它占用的一些系统资源不会被释放，比如堆栈。如果main()函数需要长时间运行，并且创建大量joinable的线程，就有可能出现堆栈不足的error . 对于那些不需要join的线程，最好利用pthread_detach，这样它运行结束后，资源就会及时得到释放。注意一个线程被使用pthread_detach之后，它就不能再被改成joinable的了.

总而言之，创建的每一个线程都应该使用pthread_join或者pthread_detach其中一个，以防止僵尸线程的出现。

相关函数：

```cpp
pthread_detach (threadid)
pthread_attr_setdetachstate (attr,detachstate)
pthread_attr_getdetachstate (attr,detachstate)
```

下面看一个例子，可以具体体会显式地设置一个线程为joinable的过程。

```cpp
#include<stdlib.h>
#include <pthread.h>
#include <stdio.h>
#include <math.h>
#define NUM_THREADS 4
void *BusyWork(void *t)
{
  int i;
  long tid;
  double result=0.0;
  tid = (long)t;
  printf("Thread %ld starting...\n",tid);
  for (i=0; i<1000000; i++)
 {
 result = result + sin(i) * tan(i);
 }
  printf("Thread %ld done. Result = %e\n",tid, result);
  pthread_exit((void*) t);
}

int main (int argc, char *argv[])
{
pthread_t thread[NUM_THREADS];
pthread_attr_t attr;
int rc;
long t;
void *status;
/* Initialize and set thread detached attribute */
pthread_attr_init(&attr);
pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);
for(t=0; t<NUM_THREADS; t++) {
printf("Main: creating thread %ld\n", t);
rc = pthread_create(&thread[t], &attr, BusyWork, (void *)t);
if (rc) {
printf("ERROR; return code from pthread_create() is %d\n", rc);
exit(-1);
 }
}

/* Free attribute and wait for the other threads */
pthread_attr_destroy(&attr);


for(t=0; t<NUM_THREADS; t++) {
rc = pthread_join(thread[t], &status);
if (rc) {
printf("ERROR; return code from pthread_join() is %d\n", rc);
exit(-1);
 }
printf("Main: completed join with thread %ld having a status of %ld\n",t,(long)status);
}
printf("Main: program completed. Exiting.\n");

pthread_exit(NULL);
}
```
输出结果：
```cpp
Main: creating thread 0
Main: creating thread 1
Thread 0 starting...
Thread 1 starting...
Thread 0 done. Result = 3.135632e+06
Main: completed join with thread 0 having a status of 0
Thread 1 done. Result = 3.135632e+06
Main: completed join with thread 1 having a status of 1
Main: program completed. Exiting.
```

## 堆栈管理 （Stack Management）

```cpp
pthread_attr_getstacksize (attr, stacksize)
pthread_attr_setstacksize (attr, stacksize)
pthread_attr_getstackaddr (attr, stackaddr)
pthread_attr_setstackaddr (attr, stackaddr)
```

POSIX标准没有规定一个线程的堆栈大小。安全可移植的程序不会依赖于具体实现默认的堆栈限制，而是显式地调用pthread_attr_setstacksize 来分配足够的堆栈空间。

下面是关于堆栈大小的一个例子.

```cpp
#include<stdlib.h>
#include <pthread.h>
#include <stdio.h>
#define NTHREADS 4
#define N 1000
#define MEGEXTRA 1000000
 
pthread_attr_t attr;

void *dowork(void *threadid)
{
   double A[N][N];
   int i,j;
   long tid;
   size_t mystacksize;

   tid = (long)threadid;
   pthread_attr_getstacksize (&attr, &mystacksize);
   printf("Thread %ld: stack size = %li bytes \n", tid, mystacksize);
   for (i=0; i<N; i++)
     for (j=0; j<N; j++)
      A[i][j] = ((i*j)/3.452) + (N-i);
   pthread_exit(NULL);
}
 
int main(int argc, char *argv[])
{
   pthread_t threads[NTHREADS];
   size_t stacksize;
   int rc;
   long t;
 
   pthread_attr_init(&attr);
   pthread_attr_getstacksize (&attr, &stacksize);
   printf("Default stack size = %li\n", stacksize);
   stacksize = sizeof(double)*N*N+MEGEXTRA;
   printf("Amount of stack needed per thread = %li\n",stacksize);
   pthread_attr_setstacksize (&attr, stacksize);
   printf("Creating threads with stack size = %li bytes\n",stacksize);
   for(t=0; t<NTHREADS; t++){
      rc = pthread_create(&threads[t], &attr, dowork, (void *)t);
      if (rc){
         printf("ERROR; return code from pthread_create() is %d\n", rc);
         exit(-1);
      }
   }
   printf("Created %ld threads.\n", t);
   pthread_exit(NULL);
}
```
输出结果：

```cpp
Default stack size = 8388608
Amount of stack needed per thread = 9000000
Creating threads with stack size = 9000000 bytes
Thread 2: stack size = 9000000 bytes 
Created 4 threads.
Thread 1: stack size = 9000000 bytes 
Thread 0: stack size = 9000000 bytes 
Thread 3: stack size = 9000000 bytes 
```


## 其他重要函数

```cpp
pthread_self ()
pthread_equal (thread1,thread2)
```

调用pthread_self可以返回它的thread ID。pthread_equal比较两个线程的ID,如果不同则返回0,否则返回一个非零值。



# 互斥锁 Mutex

Mutex常常被用来保护那些可以被多个线程访问的共享资源，比如可以防止多个线程同时更新同一个数据时出现混乱。

使用互斥锁的一般步骤是：

创建一个互斥锁，即声明一个pthread_mutex_t类型的数据,然后初始化，只有初始化之后才能使用；

多个线程尝试锁定这个互斥锁；

只有一个成功锁定互斥锁，成为互斥锁的拥有者，然后进行一些指令；

拥有者解锁互斥锁；

其他线程尝试锁定这个互斥锁，重复上面的过程；

最后互斥锁被显式地调用pthread_mutex_destroy来进行销毁。

有两种方式初始化一个互斥锁：第一种，利用已经定义的常量初始化，例如
```cpp
pthread_mutex_t mymutex = PTHREAD_MUTEX_INITIALIZER;
```

第二种方式是调用pthread_mutex_init (mutex,attr)进行初始化。

当多个线程同时去锁定同一个互斥锁时，失败的那些线程，如果是用pthread_mutex_lock函数，那么会被阻塞，直到这个互斥锁被解锁，它们再继续竞争；如果是用pthread_mutex_trylock函数，那么失败者只会返回一个错误。

最后需要指出的是，保护共享数据是程序员的责任。程序员要负责所有可以访问该数据的线程都使用mutex这种机制，否则，不使用mutex的线程还是有可能对数据造成破坏。

相关函数
```cpp
pthread_mutex_init (mutex,attr)
int pthread_mutex_destroy (pthread_mutex_t *mutex)
pthread_mutexattr_init (attr)
pthread_mutexattr_destroy (attr)
phtread_mutex_lock(pthread_mutex_t *mutex)
phtread_mutex_trylock(pthread_mutex_t *mutex)
phtread_mutex_unlock(pthread_mutex_t *mutex)
```

 下面是一个利用多线程进行向量点乘的程序。

```cpp
/*****************************************************************************
* FILE: dotprod_mutex.c
* DESCRIPTION:
*   This example program illustrates the use of mutex variables 
*   in a threads program. The main data is made available to all threads through 
*   a globally accessible  structure. Each thread works on a different 
*   part of the data. The main thread waits for all the threads to complete 
*   their computations, and then it prints the resulting sum.
* SOURCE: Vijay Sonnad, IBM
* LAST REVISED: 01/29/09 Blaise Barney
******************************************************************************/
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

/*   
The following structure contains the necessary information  
to allow the function "dotprod" to access its input data and 
place its output into the structure.  This structure is 
unchanged from the sequential version.
*/

typedef struct 
 {
   double      *a;
   double      *b;
   double     sum; 
   int     veclen; 
 } DOTDATA;

/* Define globally accessible variables and a mutex */

#define NUMTHRDS 4
#define VECLEN 100000
   DOTDATA dotstr; 
   pthread_t callThd[NUMTHRDS];
   pthread_mutex_t mutexsum;

/*
The function dotprod is activated when the thread is created.
As before, all input to this routine is obtained from a structure 
of type DOTDATA and all output from this function is written into
this structure. The benefit of this approach is apparent for the 
multi-threaded program: when a thread is created we pass a single
argument to the activated function - typically this argument
is a thread number. All  the other information required by the 
function is accessed from the globally accessible structure. 
*/

void *dotprod(void *arg)
{

/* Define and use local variables for convenience */

   int i, start, end, len ;
   long offset;
   double mysum, *x, *y;
   offset = (long)arg;
     
   len = dotstr.veclen;
   start = offset*len;
   end   = start + len;
   x = dotstr.a;
   y = dotstr.b;

/*
Perform the dot product and assign result
to the appropriate variable in the structure. 
*/
   mysum = 0;
   for (i=start; i<end ; i++)  
    {
      mysum += (x[i] * y[i]);
    }

/*
Lock a mutex prior to updating the value in the shared
structure, and unlock it upon updating.
*/
   pthread_mutex_lock (&mutexsum);
   dotstr.sum += mysum;
   printf("Thread %ld did %d to %d: mysum=%f global sum=%f\n", offset, start, end, mysum, dotstr.sum);
   pthread_mutex_unlock (&mutexsum);
   pthread_exit((void*) 0);
}

/* 
The main program creates threads which do all the work and then print out result upon completion. Before creating the threads, The input data is created. Since all threads update a shared structure, we need a mutex for mutual exclusion. The main thread needs to wait for all threads to complete, it waits for each one of the threads. We specify a thread attribute value that allow the main thread to join with the threads it creates. Note also that we free up handles  when they are no longer needed.
*/

int main (int argc, char *argv[])
{
long i;
double *a, *b;
void *status;
pthread_attr_t attr;

/* Assign storage and initialize values */

a = (double*) malloc (NUMTHRDS*VECLEN*sizeof(double));
b = (double*) malloc (NUMTHRDS*VECLEN*sizeof(double));
  
for (i=0; i<VECLEN*NUMTHRDS; i++) {
  a[i]=1;
  b[i]=a[i];
  }

dotstr.veclen = VECLEN; 
dotstr.a = a; 
dotstr.b = b; 
dotstr.sum=0;

pthread_mutex_init(&mutexsum, NULL);
         
/* Create threads to perform the dotproduct  */
pthread_attr_init(&attr);
pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);

for(i=0;i<NUMTHRDS;i++)
  {
  /* Each thread works on a different set of data.
   * The offset is specified by 'i'. The size of
   * the data for each thread is indicated by VECLEN.
   */
   pthread_create(&callThd[i], &attr, dotprod, (void *)i); 
   }

pthread_attr_destroy(&attr);
/* Wait on the other threads */

for(i=0;i<NUMTHRDS;i++) {
  pthread_join(callThd[i], &status);
  }
/* After joining, print out the results and cleanup */

printf ("Sum =  %f \n", dotstr.sum);
free (a);
free (b);
pthread_mutex_destroy(&mutexsum);
pthread_exit(NULL);
}
```

输出结果：

```cpp
Thread 0 did 0 to 100000:  mysum=100000.000000 global sum=100000.000000
Thread 1 did 100000 to 200000:  mysum=100000.000000 global sum=200000.000000
Thread 2 did 200000 to 300000:  mysum=100000.000000 global sum=300000.000000
Thread 3 did 300000 to 400000:  mysum=100000.000000 global sum=400000.000000
Sum =  400000.000000 
```


# 条件变量 Condition Variable

互斥锁只有两种状态，这限制了它的用途。条件变量允许线程在阻塞的时候等待另一个线程发送的信号，当收到信号后，阻塞的线程就被唤醒并试图锁定与之相关的互斥锁。条件变量要和互斥锁结合使用。

## 条件变量的声明和初始化

通过声明pthread_cond_t类型的数据,并且必须先初始化才能使用。

初始化的方法也有两种：

第一种，利用内部定义的常量，例如：
```cpp
pthread_cond_t myconvar = PTHREAD_COND_INITIALIZER;
```

第二种，利用函数pthread_cond_init(cond,attr),其中attr 由pthread_condattr_init() 和pthread_condattr_destroy() 创建和销毁。

用pthread_cond_destroy() 销毁一个条件变量。

相关函数：
```cpp
pthread_cond_wait (condition,mutex)
pthread_cond_signal (condition)
pthread_cond_broadcast (condition)
```
pthread_cond_wait() 会阻塞调用它的线程，直到收到某一信号。这个函数需要在mutex已经被锁之后进行调用，并且当线程被阻塞时，会自动解锁mutex. 信号收到后，线程被唤醒，这时mutex又会被这个线程锁定。

pthread_cond_signal() 函数结束时，必须解锁mutex，以供pthread_cond_wait()锁定mutex. 当不止一个线程在等待信号时，要用pthread_cond_broadcast() 代替pthread_cond_signal() 来告诉所有被该条件变量阻塞的线程结束阻塞状态。

下面是一个例子：

```cpp
/******************************************************************************
* FILE: condvar.c
* DESCRIPTION:
*   Example code for using Pthreads condition variables.  The main thread
*   creates three threads.  Two of those threads increment a "count" variable,
*   while the third thread watches the value of "count".  When "count" 
*   reaches a predefined limit, the waiting thread is signaled by one of the
*   incrementing threads. The waiting thread "awakens" and then modifies
*   count. The program continues until the incrementing threads reach
*   TCOUNT. The main program prints the final value of count.
* SOURCE: Adapted from example code in "Pthreads Programming", B. Nichols
*   et al. O'Reilly and Associates. 
* LAST REVISED: 10/14/10  Blaise Barney
******************************************************************************/
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

#define NUM_THREADS  3
#define TCOUNT 10
#define COUNT_LIMIT 12

int     count = 0;
pthread_mutex_t count_mutex;
pthread_cond_t count_threshold_cv;

void *inc_count(void *t) 
{
  int i;
  long my_id = (long)t;

  for (i=0; i < TCOUNT; i++) {
    pthread_mutex_lock(&count_mutex);
    count++;

    /* 
    Check the value of count and signal waiting thread when condition is
    reached.  Note that this occurs while mutex is locked. 
    */
    if (count == COUNT_LIMIT) {
      printf("inc_count(): thread %ld, count = %d  Threshold reached. ",
             my_id, count);
      pthread_cond_signal(&count_threshold_cv);
      printf("Just sent signal.\n");
      }
    printf("inc_count(): thread %ld, count = %d, unlocking mutex\n", 
	   my_id, count);
    pthread_mutex_unlock(&count_mutex);

    /* Do some work so threads can alternate on mutex lock */
    sleep(1);
    }
  pthread_exit(NULL);
}

void *watch_count(void *t) 
{
  long my_id = (long)t;

  printf("Starting watch_count(): thread %ld\n", my_id);

  /*
  Lock mutex and wait for signal.  Note that the pthread_cond_wait routine
  will automatically and atomically unlock mutex while it waits. 
  Also, note that if COUNT_LIMIT is reached before this routine is run by
  the waiting thread, the loop will be skipped to prevent pthread_cond_wait
  from never returning.
  */
  pthread_mutex_lock(&count_mutex);
  while (count < COUNT_LIMIT) {
    printf("watch_count(): thread %ld Count= %d. Going into wait...\n", my_id,count);
    pthread_cond_wait(&count_threshold_cv, &count_mutex);
    printf("watch_count(): thread %ld Condition signal received. Count= %d\n", my_id,count);
    printf("watch_count(): thread %ld Updating the value of count...\n", my_id,count);
    count += 125;
    printf("watch_count(): thread %ld count now = %d.\n", my_id, count);
    }
  printf("watch_count(): thread %ld Unlocking mutex.\n", my_id);
  pthread_mutex_unlock(&count_mutex);
  pthread_exit(NULL);
}

int main(int argc, char *argv[])
{
  int i, rc; 
  long t1=1, t2=2, t3=3;
  pthread_t threads[3];
  pthread_attr_t attr;

  /* Initialize mutex and condition variable objects */
  pthread_mutex_init(&count_mutex, NULL);
  pthread_cond_init (&count_threshold_cv, NULL);

  /* For portability, explicitly create threads in a joinable state */
  pthread_attr_init(&attr);
  pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);
  pthread_create(&threads[0], &attr, watch_count, (void *)t1);
  pthread_create(&threads[1], &attr, inc_count, (void *)t2);
  pthread_create(&threads[2], &attr, inc_count, (void *)t3);

  /* Wait for all threads to complete */
  for (i = 0; i < NUM_THREADS; i++) {
    pthread_join(threads[i], NULL);
  }
  printf ("Main(): Waited and joined with %d threads. Final value of count = %d. Done.\n", 
          NUM_THREADS, count);

  /* Clean up and exit */
  pthread_attr_destroy(&attr);
  pthread_mutex_destroy(&count_mutex);
  pthread_cond_destroy(&count_threshold_cv);
  pthread_exit (NULL);

}
```
输出结果：

```cpp
inc_count(): thread 3, count = 1, unlocking mutex
inc_count(): thread 2, count = 2, unlocking mutex
Starting watch_count(): thread 1
watch_count(): thread 1 Count= 2. Going into wait...
inc_count(): thread 3, count = 3, unlocking mutex
inc_count(): thread 2, count = 4, unlocking mutex
inc_count(): thread 3, count = 5, unlocking mutex
inc_count(): thread 2, count = 6, unlocking mutex
inc_count(): thread 3, count = 7, unlocking mutex
inc_count(): thread 2, count = 8, unlocking mutex
inc_count(): thread 3, count = 9, unlocking mutex
inc_count(): thread 2, count = 10, unlocking mutex
inc_count(): thread 3, count = 11, unlocking mutex
inc_count(): thread 2, count = 12  Threshold reached. Just sent signal.
inc_count(): thread 2, count = 12, unlocking mutex
watch_count(): thread 1 Condition signal received. Count= 12
watch_count(): thread 1 Updating the value of count...
watch_count(): thread 1 count now = 137.
watch_count(): thread 1 Unlocking mutex.
inc_count(): thread 3, count = 138, unlocking mutex
inc_count(): thread 2, count = 139, unlocking mutex
inc_count(): thread 3, count = 140, unlocking mutex
inc_count(): thread 2, count = 141, unlocking mutex
inc_count(): thread 3, count = 142, unlocking mutex
inc_count(): thread 2, count = 143, unlocking mutex
inc_count(): thread 3, count = 144, unlocking mutex
inc_count(): thread 2, count = 145, unlocking mutex
Main(): Waited and joined with 3 threads. Final value of count = 145. Done.
```





