Download Link: https://assignmentchef.com/product/solved-ecs150-project-2-user-level-thread-library
<br>



Introduction

Constraints

Assessment

Suggested work phases

Phase 0: Skeleton code

Phase 1: queue API

Phase 2: uthread API

Phase 3: semaphore API

Phase 4: preemption

Submission

Content

Gradescope

Academic integrity

<h1>Changelog</h1>

<strong>NOTE:</strong>

<em>The specifications for this project are subject to change at anytime for additional clarification.</em>

<em>Make sure to always refer to the <strong>latest</strong> version.</em>

v2: Fix typos v1: First publication

<h1>General information</h1>

Due before <strong>11:59 PM, Thursday, November 12th, 2020</strong>.

You will be working with a partner for this project.

The reference work environment is the CSIF.

<h1>Objectives of the project</h1>

The objectives of this programming project are:

Implementing one of the most used containers in system programming (a queue/list) as specified by a given API.

Learning how to test your code, by writing your own testers and maximizing the test coverage. Understanding how multiple threads can run within the same process: from the creation of new threads to their termination, and including how to perform context switching during their concurrent execution.

Implementing one of the most popular synchronization primitives (semaphores) as specified by a given API.

Writing high-quality C code by following established industry standards.

<h1>Program description</h1>

<h2>Introduction</h2>

The goal of this project is to understand the idea of threads, by implementing a basic user-level thread library for Linux. Your library will provide a complete interface for applications to create and run independent threads concurrently.

Similar to existing lightweight user-level thread libraries, your library must be able to:

<ol>

 <li>Create new threads</li>

 <li>Schedule the execution of threads in a round-robin fashion</li>

 <li>Be preemptive, that is to provide an interrupt-based scheduler</li>

 <li>Provide a thread synchronization API, namely semaphores</li>

</ol>

<h2>Constraints</h2>

Your code must be written in C, be compiled with GCC and only use the standard functions provided by the <a href="https://www.gnu.org/software/libc/manual/">GNU C Librar</a><a href="https://www.gnu.org/software/libc/manual/">y</a> <a href="https://www.gnu.org/software/libc/manual/">(</a><a href="https://www.gnu.org/software/libc/manual/">htt</a><a href="https://www.gnu.org/software/libc/manual/">p</a><a href="https://www.gnu.org/software/libc/manual/">s://www.</a><a href="https://www.gnu.org/software/libc/manual/">g</a><a href="https://www.gnu.org/software/libc/manual/">nu.or</a><a href="https://www.gnu.org/software/libc/manual/">g</a><a href="https://www.gnu.org/software/libc/manual/">/software/libc/manual/)</a> (aka libc). <em>All</em> the functions provided by the libc can be used, but your program cannot be linked to any other external libraries.

Your source code should adopt a sane and consistent coding style and be properly commented when <a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">necessary. One good option is to follow the relevant parts of the </a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">Linux kernel codin</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">g</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html"> st</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">y</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">le (htt</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">p</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">s://www.kernel.or</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">g</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">/doc/html/latest/</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">p</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">rocess/codin</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">g</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">-st</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">y</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">le.html)</a><a href="https://www.kernel.org/doc/html/latest/process/coding-style.html">.</a>

<h2>Assessment</h2>

Your grade for this assignment will be broken down in two scores:

<h3><strong>Auto-grading: </strong></h3>

Running an auto-grading script that tests your code and checks the output against various inputs <strong>Manual review: ~40% of grade</strong>

The manual review is itself broken down into different rubrics:

Submission : ~5%

Report file: ~40%

Makefile: ~10%

Queue implementation: ~10%

Queue testing: ~10%

Uthread library: ~15%

Code style: ~10%

<h1>Suggested work phases</h1>

<h2>Phase 0: Skeleton code</h2>

The skeleton code that you are expected to complete is available in /home/cs150jp/public/p2/. This code already defines most of the prototypes for the functions you must implement, as explained in the following sections.

<table width="673">

 <tbody>

  <tr>

   <td width="673"><strong>$</strong> cd /home/cs150jp/public/p2<strong>$</strong> tree .├── apps│   ├── Makefile│   ├── queue_tester.c│   ├── sem_buffer.c│   ├── sem_count.c│   ├── sem_prime.c│   ├── sem_simple.c│   ├── uthread_hello.c│   └── uthread_yield.c└── libuthread├── context.c├── Makefile*├── preempt.c*├── private.h├── queue.c*├── queue.h├── sem.c*├── sem.h├── uthread.c*└── uthread.h</td>

  </tr>

 </tbody>

</table>

The code is organized in two parts. In subdirectory apps, you will find several test applications. The tester queue_tester.c focuses solely on the queue implementation, and can be run after Phase 1 is completed. The testers prefixed with uthread_ make use of the thread library and require Phase 2 to be completed, while the testers prefixed with sem_ require Phase 3 to be completed.

Subdirectory libuthread contains the files composing the thread library that you must complete. The files to complete are marked with a star.

<strong>IMPORTANT:</strong>

You should have <strong>no</strong> reason to touch any of the headers which are not marked with a star (even if you think you do…).

Copy the skeleton code to your account.

<h2>Phase 1: queue API</h2>

In this first phase, you must implement a simple FIFO queue. The interface to this queue is defined in libuthread/queue.h and your code should be added into libuthread/queue.c.

You will find all the API documentation within the header file.

The constraint for this exercise is that all operations (apart from the iterate and delete operation) must be O(1). This implies that you must choose the underlying data structure for your queue implementation carefully.

<h3>Makefile</h3>

Complete the file libuthread/Makefile in order to generate a <em>static library archive</em> named libuthread/libuthread.a.

This library archive must be the default target of your Makefile, because your Makefile is called from the Makefile in the apps directory without any argument.

Note that at first, only the file libuthread/queue.c should be included in your library. You will add the other C files as you start implementing them in order to expand the API provided by your library.

Useful resources for this phase:

<u> </u><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">htt</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">p</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">://tld</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">p</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">.or</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">g</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">/HOWTO/Pro</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">g</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">ram-Librar</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">y</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">-HOWTO/static-libraries.html</a>

<a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">(htt</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">p</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">://tld</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">p</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">.or</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">g</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">/HOWTO/Pro</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">g</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">ram-Librar</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">y</a><a href="http://tldp.org/HOWTO/Program-Library-HOWTO/static-libraries.html">-HOWTO/static-libraries.html)</a>

<h3>Testing</h3>

Add a new test program in the apps directory, called queue_tester.c, which tests your queue implementation. It is important that your tester should be as comprehensive as possible in order to ensure that your queue implementation is resistant. It will ensure that you don’t encounter bugs when using your queue later on.

A good approach for testing your queue implementation is <em>unit testing</em>. The basic idea behind unit testing is to invent all the possible usage scenarios that will trigger the different parts of the implementation, and all the edge cases, in order to guarantee that the implementation always matches the specifications.

Here are two examples to get you started. The first unit test simply checks that creating a queue succeeds, while the second checks a simple enqueue/dequeue scenario:

<table width="673">

 <tbody>

  <tr>

   <td width="673">void test_create(void){     queue_t q;q = queue_create();     assert(q != NULL);}  void test_queue_simple(void){     queue_t q;     int data = 3, *ptr;q = queue_create();     queue_enqueue(q, &amp;data);     queue_dequeue(q, (void**)&amp;ptr);     assert(ptr == &amp;data);}</td>

  </tr>

 </tbody>

</table>

You can      find        a         comprehensive        example        of        a        queue        tester        in

/home/cs150jp/public/p2/apps/queue_tester_example.c, which you can complete with more unit tests.

<h3>Hints</h3>

Most of the functions of this API should look very familiar if you have ever coded a FIFO queue (e.g. create, destroy, enqueue, dequeue, etc.). However, one function of the API stands out from typical interfaces: queue_iterate(). This function provides a generic way to call a custom function (i.e. a function provided by the caller) on each item currently enqueued in the queue.

For example, the following snippet of code shows you how certain operations can be applied to every item of a queue:

<table width="673">

 <tbody>

  <tr>

   <td width="673">queue_t q; <em>/* Callback function that increments items */</em> <strong>static</strong> void inc_item(void *data){     int *a = (int*)data;     <strong>if</strong> (*a == 5)         queue_delete(q, data);     <strong>else</strong>*a += 1;}  void test_iterator(void){     int data[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};     int i; <em>/* Initialize the queue and enqueue items */</em>     q = queue_create();<strong>for</strong> (i = 0; i &lt; <strong>sizeof</strong>(data) / <strong>sizeof</strong>(data[0]); i++)         queue_enqueue(q, &amp;data[i]); <em>/* Increment every item of the queue, delete item ‘5’ */ </em>    queue_iterate(q, inc_item);     assert(data[0] == 2);     assert(queue_length(q) == 9);}</td>

  </tr>

 </tbody>

</table>

Hopefully, you will find that this function can be used in some ways when implementing the uthread

API. One interesting usage may be, for example, to debug your queue(s) of threads by printing them! But other effective usages are possible too…

<h2>Phase 2: uthread API</h2>

In this second phase, you must implement most of the thread management (some is provided to you for free). The <em>public </em>interface to this thread API is defined in libuthread/uthread.h and your code should be added into libuthread/uthread.c.

You will find all the API documentation within the header file.

<h3>Thread definition</h3>

Threads are independent execution flows that run concurrently in the address space of a single process (and thus, share the same global variables, heap memory, open files, process identifier, etc.). Each thread has its own execution context, which mainly consists of:

<ol>

 <li>a state (running, ready, blocked, etc.)</li>

 <li>a backup of the CPU registers (for saving the thread upon descheduling and restoring it later)</li>

 <li>a stack</li>

</ol>

The goal of a thread library is to provide applications that want to use multithreading an interface (i.e. a set of library functions) that the application may use to create and start new threads, terminate threads, or manipulate threads in different ways.

For example, the most well-known and wide-spread standard that defines the interface for threads on Unix-style operating systems is called <em>POSIX thread</em> (or pthread). The pthread API defines a set of functions, a subset of which we want to implement for this project. Of course, there are various ways in which the pthread API can be realized, and existing libraries have implemented pthread both in the OS kernel and in user mode. For this project, we aim to implement a few pthread functions at user level on Linux.

<h3>Public API</h3>

The API of the uthread library defines the set of functions that applications and the threads they create can call in order to interact with the library.

The first function an application has to call in order to kick off the uthread library and create the first user thread is uthread_start(). This function performs three actions:

<ol>

 <li>It registers the so-far single execution flow of the application as the <em>idle</em> thread that the library can schedule.</li>

 <li>It creates a new thread, the <em>initial thread</em>, as specified by the arguments of the function.</li>

 <li>The function finally execute an infinite loop which,

  <ol>

   <li>When there are no more threads which are ready to run in the system, stops the idle loop and exits the program.</li>

   <li>Or simply yields to next available thread.</li>

   <li>(It could also deal with threads that reached completion and destroys their associated TCB.)</li>

  </ol></li>

</ol>

Once the <em>initial thread</em> is created, it can interact with the other functions of the library, and create new threads, exit, yield, etc.

For this step, we expect the scheduler to be non-preemptive. Threads must call the function uthread_yield() in order to ask the library’s scheduler to pick and run the next available thread. In non-preemptive mode, a non-compliant thread that never yields can keep the processing resource for itself.

<h3>Private data structure</h3>

In order to deal with the creation and scheduling of threads, you will need a data structure that can store information about a single thread.

This data structure will likely need to hold, at least, information mentioned above such the context of the thread (its set of registers), information about its stack (e.g., a pointer to the thread’s stack area), and information about the state of the thread (whether it is running, ready to run, or has exited).

This data structure is often called a thread control block (<em>TCB</em>) and will be described by struct uthread_tcb.

<h3>Internal context API</h3>

Some     code     located      in libuthread/context.c,       and     which     interface     is     defined     in

libuthread/private.h, is accessible for you to use. The four functions provided by this library allow you to:

Allocate a stack when creating a new thread (and conversely, destroy a stack when a thread is finally deleted)

Initialize the stack and the execution context of the new thread so that it will run the specified function with the specified argument

Switch between two execution contexts

Useful resources if you would like to further understand how the context API works internally:

<u> </u><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">htt</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">p</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">s://www.</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">nu.or</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">/software/libc/manual/html_mono/libc.html#S</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">y</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">stem-V-contexts</a>

<a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">(htt</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">p</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">s://www.</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">nu.or</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">/software/libc/manual/html_mono/libc.html#S</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">y</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#System-V-contexts">stem-V-contexts)</a>

<h3>Testing</h3>

Two programs can help test this phase:

uthread_hello: creates a single thread that displays “Hello world!”

uthread_yield: creates three threads in cascade and test the yield feature of the scheduler

<h2>Phase 3: semaphore API</h2>

The interface of the semaphore API is defined in libuthread/sem.h and your implementation should go in libuthread/sem.c.

Semaphores are a way to control the access to common resources by multiple threads. To keep track of the number of available resource, a semaphore maintains an internal count, which can already be initialized to a certain positive value when the semaphore is created.

Threads can then ask to grab a resource (known as “down” or “P” operation) or release a resource (known as “up” or “V” operation).

Trying to grab a resource when the count of a semaphore is down to 0 adds the requesting thread to the list of threads that are waiting for this resource. The thread is put in a blocked state and shouldn’t be eligible to scheduling.

When a thread releases a semaphore which count was 0, it checks whether some other threads were currently waiting on it. In such case, the first thread of the waiting list can be unblocked, and be eligible to run later.

As you can now understand, your semaphore implementation will make use of the blocking functions defined in the private thread API.

<h3>Testing</h3>

A few testing programs are available in order to test your semaphore implementation:

sem_simple: simple example of scheduling forced by semaphores sem_count: alternate counting with two threads and two semaphores sem_buffer: producer/consumer exchanging data in a buffer sem_prime: prime sieve implemented with a growing pipeline of threads

It is recommended to look at the code of these testers and understand how they work in order to see how they will stress your semaphore implementation.

<h4>Corner case</h4>

There is a very specific corner case that you need to consider in order to implement your semaphore correctly. Here is the scenario:

Thread A calls down() on a semaphore with a count of 0, and gets blocked.

Thread B calls up() on the same semaphore, and gets thread A to be awaken

Before thread A can run again, thread C calls down() on the semaphore and snatch the newly available resource.

There are two difficulties with this scenario:

<ol>

 <li>The semaphore should make sure that thread A will handle the situation correctly when finally resuming its execution. Theoretically, it should go back to sleep if the resource is not longer available by the time it gets to run. If the thread proceeds anyway, then the semaphore implementation is incorrect.</li>

 <li>If this keeps happening, thread A will eventually be starving (i.e., it never gets access to the resource that it needs to proceed). Ideally, the semaphore implementation should prevent starvation from happening.</li>

</ol>

Think about your implementation to <em>at least</em> make it correct for that corner case, and <em>ideally</em>, find a way to avoid any starvation.

<h2>Phase 4: preemption</h2>

Up to this point, uncooperative threads could keep the processing resource for themselves if they never call uthread_yield() or never block on a semaphore.

In order to avoid such dangerous behaviour, you will add preemption to your library. The interface of the

preemption API is defined in libuthread/private.h and your code should be added to libuthread/preempt.c.

<strong>NOTE:</strong>

This preemption API is not meant to be exposed to user threads, it should stay completely transparent for them. Whenever the user code of a thread is running, preemption should be enabled.

<h3>preempt_{start,stop}()</h3>

The function preempt_start() should be called when the uthread library is initializing and sets up preemption. The setup is a two-step procedure:

<ol>

 <li>Install a signal handler that receives alarm signals (of type SIGVTALRM)</li>

 <li>Configure a timer which will fire an alarm (through a SIGVTALRM signal) a hundred times per second (i.e. 100 Hz)</li>

</ol>

Your signal handler, which acts as the timer interrupt handler, will force the currently running thread to yield, so that another thread can be scheduled instead.

The function preempt_stop() should be called before uthread_start() returns, once the multithreading phase of the application ends. It should restore the previous signal action, and restore the previous timer configuration.

Useful resources for this phase:

<u> </u><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">htt</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">p</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">s://www.</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">nu.or</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">/software/libc/manual/html_mono/libc.html#Si</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">nal-Actions (htt</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">p</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">s://www.</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">nu.or</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">/software/libc/manual/html_mono/libc.html#Si</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Signal-Actions">nal-Actions) </a><u> </u><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">htt</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">p</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">s://www.</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">nu.or</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">/software/libc/manual/html_mono/libc.html#Settin</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">-an-Alarm</a>

<a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">(htt</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">p</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">s://www.</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">nu.or</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">/software/libc/manual/html_mono/libc.html#Settin</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Setting-an-Alarm">-an-Alarm)</a>

<strong>IMPORTANT:</strong>

It is mandatory to use sigaction() over signal().

<h3>preempt_{enable,disable}()</h3>

The two other functions that you must implement are meant to enable or disable preemption. For that, you will need to be able to block or unblock signals of type SIGVTALRM.

Useful resources for this phase:

<u> </u><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">htt</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">p</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">s://www.</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">nu.or</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">/software/libc/manual/html_mono/libc.html#Blockin</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">-Si</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">nals</a>

<a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">(htt</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">p</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">s://www.</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">nu.or</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">/software/libc/manual/html_mono/libc.html#Blockin</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">-Si</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">g</a><a href="https://www.gnu.org/software/libc/manual/html_mono/libc.html#Blocking-Signals">nals)</a>

<h3>About disabling preemption…</h3>

Preemption is a great way to enable reliable and fair scheduling of threads, but it comes with some pitfalls.

For example, if the library is accessing sensitive data structures in order to add a new thread to the system and gets preempted in the middle, scheduling another thread of execution that might also manipulate the same data structures can cause the internal state of the library to become inconsistent.

Therefore, when manipulating shared data structures, preemption should be temporarily disabled so that such manipulations are guaranteed to be performed <em>atomically</em>, as critical sections.

However, avoid disabling preemption each time a thread calls the library. Try to disable preemption only when necessary. For example, the creation of a new thread can be separated between sensitive steps that need to be done atomically and non-sensitive steps that can safely be interrupted and resumed later without affecting the consistency of the shared data structures.

A good way to figure out whether preemption should be temporarily disabled while performing a sequence of operations is to imagine what would happen if this sequence was interrupted in the middle and another thread scheduled.

<h3>Testing</h3>

Add a new test program in the apps directory, called test_preempt.c, which tests the preemption.

Explain in your report why this program demonstrates that your preemptive scheduler works.

<em>Hint: the test program doesn’t have to be overly complicated…</em>


