# Tokio 

- This is an asynchronous runtime for the Rust programming language. It provides building blocks needed for writing networking applications, from large servers to small embedded devices.

## Main components

- A multi-threaded runtime for executing asynchronous code.
- An asynchronous version of the standard library.
- A large ecosystem of libraries.

## Asynchronous Rust

- Writing asynchronous code allows an application to scale much better by reducing the cost doing many things at the same time. A runtime is needed to execute Rust code in an asynchronous manner as by 
  default they are blocking APIs.

- In asynchronous programming, operations that cannot complete immediately are suspended to the background, hence thread is not blocked and can continue running other things. On completion, the 
  task is unsuspended and continues processing from where it left off.

- In asynchronous programming, there is need to track all the state necessary to resume work once the asynchronous operation completes.

- Compile-time green threading, Rust transforms async fn at compile time into a routine that operates asynchronously, the call to .await within an async fn yield control back to the thread, allowing
  it to do other work. Rust async operations are lazy.

- Async functions are called like any other function, however calling them does not resulting in the function body executing, instead calling an async fn returns a value representing the operation, conceptually
  analogous to a zero-argument closure. To actually run it, call .await operator on the return value.

- The main function used to launch the application differs from usual one is other crates as its an async fn and also annonated with #[tokio::main]. Runtime does not automatically start so the 
  main function needs to starts it. #[tokio::main] function is a macro that transforms async fn main() into a synchronous fn main() that initializes a runtime instance and executes the async main function.

- Tokio runtime has alot of functionality, TCP, UDP, Unix sockets, timers, sync utilities, multiple scheduler types, etc and not all application need the full functionality. Optimizing application requires
  only adding those features for which we use.

## Tokio advantages

- Fast
- Reliable
- Easy
- Flexible 

## Don't Tokio

- Speeding up CPU bound computations by running them in parallel on several threads. Tokio is designed for IO-bound applications where each individual task spends alot of time waiting on IO.
- Reading a lot of files, here Tokio provides no advantage compared to an ordinary threadpool, this is because the OS does not provide asynchronous file APIs.
- Sending a single web request. Tokio gives you an advantage when you want to do many things at once. 

## Concurrency

- Concurrency and parallelism are not the same thing, alternation between two tasks equals concurrency not parallelism. Tokio via its support for asynchronous code allows you to work on many tasks concurrently without 
  having to work on them in parallel using ordinary threads. Tokio can in fact run many tasks concurrently on a single thread.

## Tasks

- A Tokio task is an asynchronous green thread, they are created by passing an async block to `tokio::spawn`, which returns a JoinHandle which the caller may use to interact with the spawned task. The async block
  may have a return value which the caller may obtain using .await on the JoinHandle. Awaiting on it returns a Result or Err.

- Tasks are the unit of execution managed by the scheduler, spawning it submits it to the scheduler which then ensures that the task executes when it has work to do. The spawned task may be executed on the same thread
  as it was spawned or a different runtime thread. Task can also be moved between threads after being spawned.

- Tasks in Tokio are very lightweight, 64 bytes of memory, application should spawn as many as they want. Tasks on tokio have a 'static type lifetime, so it must not contain any references to data owned outside the task.

- By default, variables are not moved into async blocks, use move to change ownership. If a single piece of data must be accessible from more than one task then it must be shared by synchronization
  primitives such as Arc.

- Tasks spawned by tokio::spawn must implement Send, this allows Tokio to move the tasks between threads while they are suspended at an .await. Tasks are Send when all data that is held across .await calls is Send, 
  conversely if the state is not Send, then neither is the task. 
  
## Shared State

- State is shared in Tokio via: Guard the shared state with a Mutex, Spawn a task to manage the state and use message passing to operate on it.
- Using a blocking mutex to guard short critical sections is an acceptable strategy when contention is minimal, when a lock is contended, the thread executing the task must block and wait on the mutex. This will not only block 
  the current task but it will also block all other tasks scheduled on the current thread.
- If contention on a synchronous mutex becomes a problem, the best fix is rarely to switch to the Tokio mutex, considerations include switching to a dedicated task to manage state and use message passing, shard the mutex
  or restructure the code to avoid the mutex.
  
## Channels

- Tokio provides a couple of channel:
  - mpsc: multi-producer, single consumer, many values can be sent.
  - oneshot: one producer, one consumer channel, single value can be sent.
  - broadcast: multi-producer, multi-consumer, many values can be sent, each receiver sees every value.
  - watch: single producer, multi consumer, many values can be sent but no history is kept, receivers only see the most recent value.
  
## I/O

## Framing