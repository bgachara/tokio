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
