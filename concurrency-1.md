# Approach to Concurrency

## Concurrency with multiple processes
Divide processes within an application into multiple, separate, single-threaded process that are run at the same time.

Feature: Pass messages through interprocess communication channels.

Cons: 
1. Such communication is either complicated to set up or slow or both because OS provides protection between processes to avoid one process accidentally modifying data belonging to another process.
2. There is an inherent overhead in running multiple process -> it takes time to start a process, the OS devote internal resources to managing the process.

Pros: 
1. The added protections -> it's much easier to write `safe`concurrent code with processes rather than threads.
2. Using seperate processes for currency has another advantage -> you can run seperate process on distinct machines connected over the network.
## Concurrency with multiple threads
You can also run multiple threads within a process.

Feature: all threads in a process share the same address space, and most all the data can be accessed directly from all threads—global variables remain global, and pointers or references to objects or data can be passed around among threads.

Cons: Lack of protection between threads, and the programmer must ensure that the view of data seen by each thread is consistent whenever it is accessed.

Pros: Smaller overheadsa and flexibility of shared memory.
# Why Use Concurrency
1. Seperation of Concerns
2. Performance

## Seperation of Concerns
Seperate distinct area of functionality, even when the operations in these distinct areas need to happen at the same time.

In this case, # of threads is indepedent of the # of CPU cores available, because divides into multiple threads is based on conceptual design rather than an attempt to increase the throughput.

## Performance
There are two ways to use concurrency for performance. 
1. `Task parallelism` is to divide a single task into parts and run each in parallel, thus reducing the total runtime. Although this sounds straightforward, it can be quite a complex process, because there may be many dependencies between the various parts. 
2. The divisions may be either in terms of processing—one thread performs one part of the algorithm while another thread performs a different part—or in terms of data—each thread performs the `same operation` on `different parts of the data`. This latter approach is called `data parallelism`.

Algorithms that are readily susceptible to such parallelism are frequently called `embarrassingly parallel`.

Embarrassingly parallel algorithms have good `scalability` properties—as the number of available hardware threads goes up, the parallelism in the algorithm can be increased to match. Such an algorithm is the perfect embodiment of the adage, “Many hands make light work.” 

For those parts of the algorithm that aren’t embarrassingly parallel, you might be able to divide the algorithm into a fixed (and therefore not scalable) number of parallel tasks.

The second way to use concurrency for performance is to use the available parallelism to solve bigger problems; rather than processing one file at a time, process 2 or 10 or 20, as appropriate. Although this is really just an application of data parallelism, by performing the same operation on multiple sets of data concurrently, there’s a different focus. 

It still takes the same amount of time to process one chunk of data, but now `more data can be processed in the same amount of time`. Obviously, there are limits to this approach too, and this won’t be beneficial in all cases, but the increase in throughput that comes from such an approach can actually make new things possible—increased resolution in video processing, for example, if different areas of the picture can be processed in parallel.

# When NOT to Use Concurrency
Fundamentally, the only reason not to use concurrency is when the `benefit is not worth the cost`. Unless the potential performance gain is large enough or separation of concerns clear enough to justify the additional development time required to get it right and the additional costs associated with maintaining multithreaded code, don’t use concurrency.

## Too Many Threads Issues

Also, the performance gain might not be as large as expected; there’s an inherent `overhead` associated with launching a thread, because the OS has to allocate the associated kernel resources and stack space and then add the new thread to the scheduler, all of which takes time. If the task being run on the thread is completed quickly, the actual time taken by the task may be dwarfed by the overhead of launching the thread, possibly making the overall performance of the application worse than if the task had been executed directly by the spawning thread.

Furthermore, threads are a `limited resource`. If you have too many threads running at once, this consumes OS resources and may make the system as a whole run slower. Not only that, but using too many threads can `exhaust` the available memory or address space for a process, because each thread requires a separate stack space.

If the server side of a client/server application launches a separate thread for each connection, this works fine for a small number of connections, but can quickly exhaust system resources by launching too many threads if the same technique is used for a high-demand server that has to handle many connections. In this scenario, careful use of thread pools can provide optimal performance (see chapter 9).

Finally, the more threads you have running, the more `context switching` the operating system has to do. Each context switch takes time that could be spent doing useful work, so at some point adding an extra thread will actually reduce the overall application performance rather than increase it.