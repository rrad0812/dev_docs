# Go By Example II

[Sadržaj][00]

## 26 Errors

In Go it’s idiomatic to communicate errors via an explicit, separate return value. This contrasts with the exceptions used in languages like Java, Python and Ruby and the overloaded single result / error value sometimes used in C. Go’s approach makes it easy to see which functions return errors and to handle them using the same language constructs employed for other, non-error tasks.

See the documentation of the errors package and this blog post for additional details.

```go
package main

import (
    "errors"
    "fmt"
)

// By convention, errors are the last return value and have type error, a built-in interface.
func f(arg int) (int, error) {
    if arg == 42 {

        // errors.New constructs a basic error value with the given error message.
        return -1, errors.New("can't work with 42")
    }

    // A nil value in the error position indicates that there was no error.
    return arg + 3, nil
}

// A sentinel error is a predeclared variable that is used to signify a specific error condition.

var ErrOutOfTea = errors.New("no more tea available")
var ErrPower = errors.New("can't boil water")

func makeTea(arg int) error {
    if arg == 2 {
        return ErrOutOfTea
    } else if arg == 4 {

        // We can wrap errors with higher-level errors to add context. The simplest way to do this 
        // is with the %w verb in fmt.Errorf. Wrapped errors create a logical chain (A wraps B, 
        // which wraps C, etc.) that can be queried with functions like errors.Is and errors.AsType.
        return fmt.Errorf("making tea: %w", ErrPower)
    }
    return nil
}

func main() {
    for _, i := range []int{7, 42} {

        // It’s idiomatic to use an inline error check in the if line.
        if r, e := f(i); e != nil {
            fmt.Println("f failed:", e)
        } else {
            fmt.Println("f worked:", r)
        }
    }

    for i := range 5 {
        if err := makeTea(i); err != nil {

            // errors.Is checks that a given error (or any error in its chain) matches a specific 
            // error value. This is especially useful with wrapped or nested errors, allowing you 
            // to identify specific error types or sentinel errors in a chain of errors.
            if errors.Is(err, ErrOutOfTea) {
                fmt.Println("We should buy new tea!")
            } else if errors.Is(err, ErrPower) {
                fmt.Println("Now it is dark.")
            } else {
                fmt.Printf("unknown error: %s\n", err)
            }
            continue
        }
        fmt.Println("Tea is ready!")
    }
}
```

```sh
go run errors.go
f worked: 10
f failed: can't work with 42
Tea is ready!
Tea is ready!
We should buy new tea!
Tea is ready!
Now it is dark.
```

[Sadržaj][00]

## 27 Custom Errors

It’s possible to define custom error types by implementing the Error() method on them. Here’s a variant on the example above that uses a custom type to explicitly represent an argument error.

```go
package main

import (
    "errors"
    "fmt"
)

// A custom error type usually has the suffix “Error”.
type argError struct {
    arg     int
    message string
}

// Adding this Error method makes argError implement the error interface.
func (e *argError) Error() string {
    return fmt.Sprintf("%d - %s", e.arg, e.message)
}

func f(arg int) (int, error) {
    if arg == 42 {

        // Return our custom error.
        return -1, &argError{arg, "can't work with it"}
    }
    return arg + 3, nil
}

func main() {

    // errors.AsType is a more advanced version of errors.Is. It checks that a given error (or any 
    // error in its chain) matches a specific error type and converts to a value of that type, also 
    // returning true. If there’s no match, the second return value is false.
    _, err := f(42)
    if ae, ok := errors.AsType[*argError](err); ok {
        fmt.Println(ae.arg)
        fmt.Println(ae.message)
    } else {
        fmt.Println("err doesn't match argError")
    }
}
```

```sh
go run custom-errors.go
42
can't work with it
```

[Sadržaj][00]

## 28 Goroutines

A goroutine is a lightweight thread of execution.

```go
package main

import (
    "fmt"
    "time"
)

func f(from string) {
    for i := range 3 {
        fmt.Println(from, ":", i)
    }
}

func main() {

    // Suppose we have a function call f(s). Here’s how we’d call that in the usual way, running it 
    // synchronously.
    f("direct")

    // To invoke this function in a goroutine, use go f(s). This new goroutine will execute 
    // concurrently with the calling one.
    go f("goroutine")

    // You can also start a goroutine for an anonymous function call.
    go func(msg string) {
        fmt.Println(msg)
    }("going")

    // Our two function calls are running asynchronously in separate goroutines now. Wait for them 
    // to finish (for a more robust approach, use a WaitGroup).
    time.Sleep(time.Second)
    fmt.Println("done")
}
```

When we run this program, we see the output of the blocking call first, then the output of the two goroutines. The goroutines’ output may be interleaved, because goroutines are being run concurrently by the Go runtime.

```sh
go run goroutines.go
direct : 0
direct : 1
direct : 2
goroutine : 0
going
goroutine : 1
goroutine : 2
done
```

[Sadržaj][00]

## 29 Channels

Channels are the pipes that connect concurrent goroutines. You can send values into channels from one goroutine and receive those values into another goroutine.

```go
package main

import "fmt"

func main() {

    // Create a new channel with make(chan val-type). Channels are typed by the values they convey.
    messages := make(chan string)

    // Send a value into a channel using the channel <- syntax. Here we send "ping" to the messages 
    // channel we made above, from a new goroutine.
    go func() { messages <- "ping" }()

    // The <-channel syntax receives a value from the channel. Here we’ll receive the "ping" 
    // message we sent above and print it out.
    msg := <-messages
    fmt.Println(msg)
}
```

When we run the program the "ping" message is successfully passed from one goroutine to another via our channel.

```sh
go run channels.go 
ping
```

By default sends and receives block until both the sender and receiver are ready. This property allowed us to wait at the end of our program for the "ping" message without having to use any other synchronization.

[Sadržaj][00]

## 30 Channel Buffering

By default channels are unbuffered, meaning that they will only accept sends (chan <-) if there is a corresponding receive (<- chan) ready to receive the sent value. Buffered channels accept a limited number of values without a corresponding receiver for those values.

```go
package main

import "fmt"

func main() {

    // Here we make a channel of strings buffering up to 2 values.
    messages := make(chan string, 2)

    // Because this channel is buffered, we can send these values into the channel without a 
    // corresponding concurrent receive.
    messages <- "buffered"
    messages <- "channel"

    // Later we can receive these two values as usual.
    fmt.Println(<-messages)
    fmt.Println(<-messages)
}
```

```sh
go run channel-buffering.go 
buffered
channel
```

[Sadržaj][00]

## 31 Channel Synchronization

We can use channels to synchronize execution across goroutines. Here’s an example of using a blocking receive to wait for a goroutine to finish. When waiting for multiple goroutines to finish, you may prefer to use a WaitGroup.

```go
package main

import (
    "fmt"
    "time"
)

// This is the function we’ll run in a goroutine. The done channel will be used to notify another 
// goroutine that this function’s work is done.
func worker(done chan bool) {
    fmt.Print("working...")
    time.Sleep(time.Second)
    fmt.Println("done")

    // Send a value to notify that we’re done.
    done <- true
}

func main() {

    // Start a worker goroutine, giving it the channel to notify on.
    done := make(chan bool, 1)
    go worker(done)

    // Block until we receive a notification from the worker on the channel.
    <-done
}
```

```sh
go run channel-synchronization.go      
working...done                  
```

If you removed the <- done line from this program, the program could exit before the worker finished its work, or in some cases even before it started.

[Sadržaj][00]

## 32 Channel Directions

When using channels as function parameters, you can specify if a channel is meant to only send or receive values. This specificity increases the type-safety of the program.

```go
package main

import "fmt"

// This ping function only accepts a channel for sending values. It would be a compile-time error 
// to try to receive on this channel.
func ping(pings chan<- string, msg string) {
    pings <- msg
}

// The pong function accepts one channel for receives (pings) and a second for sends (pongs).
func pong(pings <-chan string, pongs chan<- string) {
    msg := <-pings
    pongs <- msg
}

func main() {
    pings := make(chan string, 1)
    pongs := make(chan string, 1)
    ping(pings, "passed message")
    pong(pings, pongs)
    fmt.Println(<-pongs)
}
```

```sh
go run channel-directions.go
passed message
```

[Sadržaj][00]

## 33 Select

Go’s select lets you wait on multiple channel operations. Combining goroutines and channels with select is a powerful feature of Go.

```go
package main

import (
    "fmt"
    "time"
)

func main() {

    // For our example we’ll select across two channels.
    c1 := make(chan string)
    c2 := make(chan string)

    // Each channel will receive a value after some amount of time, to simulate e.g. blocking RPC 
    // operations executing in concurrent goroutines.
    go func() {
        time.Sleep(1 * time.Second)
        c1 <- "one"
    }()

    go func() {
        time.Sleep(2 * time.Second)
        c2 <- "two"
    }()

    // We’ll use select to await both of these values simultaneously, printing each one as it 
    // arrives.
    for range 2 {
        select {
        case msg1 := <-c1:
            fmt.Println("received", msg1)
        case msg2 := <-c2:
            fmt.Println("received", msg2)
        }
    }
}
```

We receive the values "one" and then "two" as expected.

```sh
time go run select.go 
received one
received two
```

Note that the total execution time is only ~2 seconds since both the 1 and 2 second Sleeps
execute concurrently.

```sh
real    0m2.245s
```

[Sadržaj][00]

## 34 Timeouts

Timeouts are important for programs that connect to external resources or that otherwise need to bound execution time. Implementing timeouts in Go is easy and elegant thanks to channels and select.

```go
package main

import (
    "fmt"
    "time"
)

func main() {

    // For our example, suppose we’re executing an external call that returns its result on a 
    // channel c1 after 2s. Note that the channel is buffered, so the send in the goroutine is 
    // nonblocking. This is a common pattern to prevent goroutine leaks in case the channel is 
    // never read.
    c1 := make(chan string, 1)
    go func() {
        time.Sleep(2 * time.Second)
        c1 <- "result 1"
    }()

    // Here’s the select implementing a timeout. res := <-c1 awaits the result and <-time.After 
    // awaits a value to be sent after the timeout of 1s. Since select proceeds with the first 
    // receive that’s ready, we’ll take the timeout case if the operation takes more than the 
    // allowed 1s.
    select {
    case res := <-c1:
        fmt.Println(res)
    case <-time.After(1 * time.Second):
        fmt.Println("timeout 1")
    }

    // If we allow a longer timeout of 3s, then the receive from c2 will succeed and we’ll print 
    // the result.
    c2 := make(chan string, 1)
    go func() {
        time.Sleep(2 * time.Second)
        c2 <- "result 2"
    }()
    select {
    case res := <-c2:
        fmt.Println(res)
    case <-time.After(3 * time.Second):
        fmt.Println("timeout 2")
    }
}
```

Running this program shows the first operation timing out and the second succeeding.

```sh
go run timeouts.go 
timeout 1
result 2
```

[Sadržaj][00]

## 35 Non-Blocking Channel Operations

Basic sends and receives on channels are blocking. However, we can use select with a default clause to implement non-blocking sends, receives, and even non-blocking multi-way selects.

```go
package main

import "fmt"

func main() {
    messages := make(chan string)
    signals := make(chan bool)

    // Here’s a non-blocking receive. If a value is available on messages then select will take the 
    // <-messages case with that value. If not it will immediately take the default case.
    select {
    case msg := <-messages:
        fmt.Println("received message", msg)
    default:
        fmt.Println("no message received")
    }

    // A non-blocking send works similarly. Here msg cannot be sent to the messages channel, 
    // because the channel has no buffer and there is no receiver. Therefore the default case is 
    // selected.
    msg := "hi"
    select {
    case messages <- msg:
        fmt.Println("sent message", msg)
    default:
        fmt.Println("no message sent")
    }

    // We can use multiple cases above the default clause to implement a multi-way non-blocking 
    // select. Here we attempt non-blocking receives on both messages and signals.
    select {
    case msg := <-messages:
        fmt.Println("received message", msg)
    case sig := <-signals:
        fmt.Println("received signal", sig)
    default:
        fmt.Println("no activity")
    }
}
```

```sh
go run non-blocking-channel-operations.go 
no message received
no message sent
no activity
```

[Sadržaj][00]

## 36 Closing Channels

Closing a channel indicates that no more values will be sent on it. This can be useful to communicate completion to the channel’s receivers.

```go
package main

import "fmt"

// In this example we’ll use a jobs channel to communicate work to be done from the main() 
// goroutine to a worker goroutine. When we have no more jobs for the worker we’ll close the jobs 
// channel.

func main() {
    jobs := make(chan int, 5)
    done := make(chan bool)

    // Here’s the worker goroutine. It repeatedly receives from jobs with j, more := <-jobs. In 
    // this special 2-value form of receive, the more value will be false if jobs has been closed 
    // and all values in the channel have already been received. We use this to notify on done when 
    // we’ve worked all our jobs.
    go func() {
        for {
            j, more := <-jobs
            if more {
                fmt.Println("received job", j)
            } else {
                fmt.Println("received all jobs")
                done <- true
                return
            }
        }
    }()

    // This sends 3 jobs to the worker over the jobs channel, then closes it.
    for j := 1; j <= 3; j++ {
        jobs <- j
        fmt.Println("sent job", j)
    }
    close(jobs)
    fmt.Println("sent all jobs")

    // We await the worker using the synchronization approach we saw earlier.
    <-done

    // Reading from a closed channel succeeds immediately, returning the zero value of the 
    // underlying type. The optional second return value is true if the value received was 
    // delivered by a successful send operation to the channel, or false if it was a zero value 
    // generated because the channel is closed and empty.
    _, ok := <-jobs
    fmt.Println("received more jobs:", ok)
}
```

```sh
go run closing-channels.go 
sent job 1
received job 1
sent job 2
received job 2
sent job 3
received job 3
sent all jobs
received all jobs
received more jobs: false
```

The idea of closed channels leads naturally to our next example: range over channels.

[Sadržaj][00]

## 37 Range over Channels

In a previous example we saw how for and range provide iteration over basic data structures. We can also use this syntax to iterate over values received from a channel.

```go
package main

import "fmt"

func main() {

    // We’ll iterate over 2 values in the queue channel.
    queue := make(chan string, 2)
    queue <- "one"
    queue <- "two"
    close(queue)

    // This range iterates over each element as it’s received from queue. Because we closed the 
    // channel above, the iteration terminates after receiving the 2 elements.
    for elem := range queue {
        fmt.Println(elem)
    }
}
```

```sh
go run range-over-channels.go
one
two
```

This example also showed that it’s possible to close a non-empty channel but still have the remaining values be received.

[Sadržaj][00]

## 38 Timers

We often want to execute Go code at some point in the future, or repeatedly at some interval. Go’s built-in timer and ticker features make both of these tasks easy. We’ll look first at timers and then at tickers.

```go
package main

import (
    "fmt"
    "time"
)

func main() {

    // Timers represent a single event in the future. You tell the timer how long you want to wait, 
    // and it provides a channel that will be notified at that time. This timer will wait 2 seconds.
    timer1 := time.NewTimer(2 * time.Second)

    // The <-timer1.C blocks on the timer’s channel C until it sends a value indicating that the 
    // timer fired.
    <-timer1.C
    fmt.Println("Timer 1 fired")

    // If you just wanted to wait, you could have used time.Sleep. One reason a timer may be useful 
    // is that you can cancel the timer before it fires. Here’s an example of that.
    timer2 := time.NewTimer(time.Second)
    go func() {
        <-timer2.C
        fmt.Println("Timer 2 fired")
    }()
    stop2 := timer2.Stop()
    if stop2 {
        fmt.Println("Timer 2 stopped")
    }

    // Give the timer2 enough time to fire, if it ever was going to, to show it is in fact stopped.
    time.Sleep(2 * time.Second)
}
```

The first timer will fire ~2s after we start the program, but the second should be stopped before it has a chance to fire.

```sh
go run timers.go
Timer 1 fired
Timer 2 stopped
```

[Sadržaj][00]

## 39 Tickers

Timers are for when you want to do something once in the future - tickers are for when you want to do something repeatedly at regular intervals. Here’s an example of a ticker that ticks periodically until we stop it.

```go
package main

import (
    "fmt"
    "time"
)

func main() {

    // Tickers use a similar mechanism to timers: a channel that is sent values. Here we’ll use the 
    // select builtin on the channel to await the values as they arrive every 500ms.
    ticker := time.NewTicker(500 * time.Millisecond)
    done := make(chan bool)

    go func() {
        for {
            select {
            case <-done:
                return
            case t := <-ticker.C:
                fmt.Println("Tick at", t)
            }
        }
    }()

    // Tickers can be stopped like timers. Once a ticker is stopped it won’t receive any more 
    // values on its channel. We’ll stop ours after 1600ms.
    time.Sleep(1600 * time.Millisecond)
    ticker.Stop()
    done <- true
    fmt.Println("Ticker stopped")
}
```

When we run this program the ticker should tick 3 times before we stop it.

```sh
go run tickers.go
Tick at 2012-09-23 11:29:56.487625 -0700 PDT
Tick at 2012-09-23 11:29:56.988063 -0700 PDT
Tick at 2012-09-23 11:29:57.488076 -0700 PDT
Ticker stopped
```

[Sadržaj][00]

## 40 Worker Pools

In this example we’ll look at how to implement a worker pool using goroutines and channels.

```go
package main

import (
    "fmt"
    "time"
)

// Here’s the worker, of which we’ll run several concurrent instances. These workers will receive 
// work on the jobs channel and send the corresponding results on results. We’ll sleep a second per 
// job to simulate an expensive task.
func worker(id int, jobs <-chan int, results chan<- int) {
    for j := range jobs {
        fmt.Println("worker", id, "started  job", j)
        time.Sleep(time.Second)
        fmt.Println("worker", id, "finished job", j)
        results <- j * 2
    }
}

func main() {

    // In order to use our pool of workers we need to send them work and collect their results. We 
    // make 2 channels for this.
    const numJobs = 5
    jobs := make(chan int, numJobs)
    results := make(chan int, numJobs)

    // This starts up 3 workers, initially blocked because there are no jobs yet.
    for w := 1; w <= 3; w++ {
        go worker(w, jobs, results)
    }

    // Here we send 5 jobs and then close that channel to indicate that’s all the work we have.
    for j := 1; j <= numJobs; j++ {
        jobs <- j
    }
    close(jobs)

    // Finally we collect all the results of the work. This also ensures that the worker goroutines 
    // have finished. An alternative way to wait for multiple goroutines is to use a WaitGroup.
    for a := 1; a <= numJobs; a++ {
        <-results
    }
}
```

Our running program shows the 5 jobs being executed by various workers. The program only takes about 2 seconds despite doing about 5 seconds of total work because there are 3 workers operating concurrently.

```sh
time go run worker-pools.go 
worker 1 started  job 1
worker 2 started  job 2
worker 3 started  job 3
worker 1 finished job 1
worker 1 started  job 4
worker 2 finished job 2
worker 2 started  job 5
worker 3 finished job 3
worker 1 finished job 4
worker 2 finished job 5

real    0m2.358s
```

[Sadržaj][00]

## 41 WaitGroups

To wait for multiple goroutines to finish, we can use a wait group.

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

// This is the function we’ll run in every goroutine.
func worker(id int) {
    fmt.Printf("Worker %d starting\n", id)

    // Sleep to simulate an expensive task.
    time.Sleep(time.Second)
    fmt.Printf("Worker %d done\n", id)
}

func main() {

    // This WaitGroup is used to wait for all the goroutines launched here to finish. Note: if a 
    // WaitGroup is explicitly passed into functions, it should be done by pointer.
    var wg sync.WaitGroup

    // Launch several goroutines using WaitGroup.Go
    for i := 1; i <= 5; i++ {
        wg.Go(func() {
            worker(i)
        })
    }

    // Block until all the goroutines started by wg are done. A goroutine is done when the function 
    // it invokes returns.
    wg.Wait()

    // Note that this approach has no straightforward way to propagate errors from workers. For 
    // more advanced use cases, consider using the errgroup package.
}
```

```sh
go run waitgroups.go
Worker 5 starting
Worker 3 starting
Worker 4 starting
Worker 1 starting
Worker 2 starting
Worker 4 done
Worker 1 done
Worker 2 done
Worker 5 done
Worker 3 done
```

The order of workers starting up and finishing is likely to be different for each invocation.

[Sadržaj][00]

## 42 Rate Limiting

Rate limiting is an important mechanism for controlling resource utilization and maintaining quality of service. Go elegantly supports rate limiting with goroutines, channels, and tickers.

```go
package main

import (
    "fmt"
    "time"
)

func main() {

    // First we’ll look at basic rate limiting. Suppose we want to limit our handling of incoming 
    // requests. We’ll serve these requests off a channel of the same name.
    requests := make(chan int, 5)
    for i := 1; i <= 5; i++ {
        requests <- i
    }
    close(requests)

    // This limiter channel will receive a value every 200 milliseconds. This is the regulator in 
    // our rate limiting scheme.
    limiter := time.Tick(200 * time.Millisecond)

    // By blocking on a receive from the limiter channel before serving each request, we limit 
    // ourselves to 1 request every 200 milliseconds.
    for req := range requests {
        <-limiter
        fmt.Println("request", req, time.Now())
    }

    // We may want to allow short bursts of requests in our rate limiting scheme while preserving 
    // the overall rate limit. We can accomplish this by buffering our limiter channel. This 
    // burstyLimiter channel will allow bursts of up to 3 events.
    burstyLimiter := make(chan time.Time, 3)

    // Fill up the channel to represent allowed bursting.
    for range 3 {
        burstyLimiter <- time.Now()
    }

    // Every 200 milliseconds we’ll try to add a new value to burstyLimiter, up to its limit of 3.
    go func() {
        for t := range time.Tick(200 * time.Millisecond) {
            burstyLimiter <- t
        }
    }()

    // Now simulate 5 more incoming requests. The first 3 of these will benefit from the burst 
    // capability of burstyLimiter.
    burstyRequests := make(chan int, 5)
    for i := 1; i <= 5; i++ {
        burstyRequests <- i
    }
    close(burstyRequests)
    for req := range burstyRequests {
        <-burstyLimiter
        fmt.Println("request", req, time.Now())
    }
}
```

Running our program we see the first batch of requests handled once every ~200 milliseconds as desired.

```sh
go run rate-limiting.go
request 1 2012-10-19 00:38:18.687438 +0000 UTC
request 2 2012-10-19 00:38:18.887471 +0000 UTC
request 3 2012-10-19 00:38:19.087238 +0000 UTC
request 4 2012-10-19 00:38:19.287338 +0000 UTC
request 5 2012-10-19 00:38:19.487331 +0000 UTC
```

For the second batch of requests we serve the first 3 immediately because of the burstable rate limiting, then serve the remaining 2 with ~200ms delays each.

```sh
request 1 2012-10-19 00:38:20.487578 +0000 UTC
request 2 2012-10-19 00:38:20.487645 +0000 UTC
request 3 2012-10-19 00:38:20.487676 +0000 UTC
request 4 2012-10-19 00:38:20.687483 +0000 UTC
request 5 2012-10-19 00:38:20.887542 +0000 UTC
```

[Sadržaj][00]

## 43 Atomic Counters

The primary mechanism for managing state in Go is communication over channels. We saw this for example with worker pools. There are a few other options for managing state though. Here we’ll look at using the sync/atomic package for atomic counters accessed by multiple goroutines.

```go
package main

import (
    "fmt"
    "sync"
    "sync/atomic"
)

func main() {

    // We’ll use an atomic integer type to represent our (always-positive) counter.
    var ops atomic.Uint64

    // A WaitGroup will help us wait for all goroutines to finish their work.
    var wg sync.WaitGroup

    // We’ll start 50 goroutines that each increment the counter exactly 1000 times.
    for range 50 {
        wg.Go(func() {
            for range 1000 {

                // To atomically increment the counter we use Add.
                ops.Add(1)
            }
        })
    }

    // Wait until all the goroutines are done.
    wg.Wait()

    // Here no goroutines are writing to ‘ops’, but using Load it’s safe to atomically read a value 
    // even while other goroutines are (atomically) updating it.
    fmt.Println("ops:", ops.Load())
}
```

We expect to get exactly 50,000 operations. Had we used a non-atomic integer and incremented it with ops++, we’d likely get a different number, changing between runs, because the goroutines would interfere with each other. Moreover, we’d get data race failures when running with the -race flag.

```sh
go run atomic-counters.go
ops: 50000
```

Next we’ll look at mutexes, another tool for managing state.

[Sadržaj][00]

## 44 Mutexes

In the previous example we saw how to manage simple counter state using atomic operations. For more complex state we can use a mutex to safely access data across multiple goroutines.

```go
package main

import (
    "fmt"
    "sync"
)

// Container holds a map of counters; since we want to update it concurrently from multiple 
// goroutines, we add a Mutex to synchronize access. Note that mutexes must not be copied, so if 
// this struct is passed around, it should be done by pointer.
type Container struct {
    mu       sync.Mutex
    counters map[string]int
}

// Lock the mutex before accessing counters; unlock it at the end of the function using a defer 
// statement.
func (c *Container) inc(name string) {

    c.mu.Lock()
    defer c.mu.Unlock()
    c.counters[name]++
}

// Note that the zero value of a mutex is usable as-is, so no initialization is required here.

func main() {
    c := Container{
        counters: map[string]int{"a": 0, "b": 0},
    }

    var wg sync.WaitGroup

    // This function increments a named counter in a loop.
    doIncrement := func(name string, n int) {
        for range n {
            c.inc(name)
        }
    }

    // Run several goroutines concurrently; note that they all access the same Container, and two 
    // of them access the same counter.
    wg.Go(func() {
        doIncrement("a", 10000)
    })

    wg.Go(func() {
        doIncrement("a", 10000)
    })

    wg.Go(func() {
        doIncrement("b", 10000)
    })

    // Wait for the goroutines to finish
    wg.Wait()
    fmt.Println(c.counters)
}
```

Running the program shows that the counters updated as expected.

```sh
go run mutexes.go
map[a:20000 b:10000]
```

Next we’ll look at implementing this same state management task using only goroutines and channels.

[Sadržaj][00]

## 45 Stateful Goroutines

In the previous example we used explicit locking with mutexes to synchronize access to shared state across multiple goroutines. Another option is to use the built-in synchronization features of goroutines and channels to achieve the same result. This channel-based approach aligns with Go’s ideas of sharing memory by communicating and having each piece of data owned by exactly one goroutine.

```go
package main

import (
    "fmt"
    "math/rand"
    "sync/atomic"
    "time"
)

// In this example our state will be owned by a single goroutine. This will guarantee that the data 
// is never corrupted with concurrent access. In order to read or write that state, other 
// goroutines will send messages to the owning goroutine and receive corresponding replies. These 
// readOp and writeOp structs encapsulate those requests and a way for the owning goroutine to 
// respond.
type readOp struct {
    key  int
    resp chan int
}

type writeOp struct {
    key  int
    val  int
    resp chan bool
}

func main() {

    // As before we’ll count how many operations we perform.
    var readOps uint64
    var writeOps uint64

    // The reads and writes channels will be used by other goroutines to issue read and write requests, respectively.
    reads := make(chan readOp)
    writes := make(chan writeOp)

    // Here is the goroutine that owns the state, which is a map as in the previous example but now 
    // private to the stateful goroutine. This goroutine repeatedly selects on the reads and writes 
    // channels, responding to requests as they arrive. A response is executed by first performing 
    // the requested operation and then sending a value on the response channel resp to indicate 
    // success (and the desired value in the case of reads).
    go func() {
        var state = make(map[int]int)
        for {
            select {
            case read := <-reads:
                read.resp <- state[read.key]
            case write := <-writes:
                state[write.key] = write.val
                write.resp <- true
            }
        }
    }()

    // This starts 100 goroutines to issue reads to the state-owning goroutine via the reads 
    // channel. Each read requires constructing a readOp, sending it over the reads channel, and 
    // then receiving the result over the provided resp channel.
    for range 100 {
        go func() {
            for {
                read := readOp{
                    key:  rand.Intn(5),
                    resp: make(chan int)}
                reads <- read
                <-read.resp
                atomic.AddUint64(&readOps, 1)
                time.Sleep(time.Millisecond)
            }
        }()
    }

    // We start 10 writes as well, using a similar approach.
    for range 10 {
        go func() {
            for {
                write := writeOp{
                    key:  rand.Intn(5),
                    val:  rand.Intn(100),
                    resp: make(chan bool)}
                writes <- write
                <-write.resp
                atomic.AddUint64(&writeOps, 1)
                time.Sleep(time.Millisecond)
            }
        }()
    }

    // Let the goroutines work for a second.
    time.Sleep(time.Second)

    // Finally, capture and report the op counts.
    readOpsFinal := atomic.LoadUint64(&readOps)
    fmt.Println("readOps:", readOpsFinal)
    writeOpsFinal := atomic.LoadUint64(&writeOps)
    fmt.Println("writeOps:", writeOpsFinal)
}
```

Running our program shows that the goroutine-based state management example completes about 80,000 total operations.

```sh
go run stateful-goroutines.go
readOps: 71708
writeOps: 7177
```

For this particular case the goroutine-based approach was a bit more involved than the mutex-based one. It might be useful in certain cases though, for example where you have other channels involved or when managing multiple such mutexes would be error-prone. You should use whichever approach feels most natural, especially with respect to understanding the correctness of your program.

[Sadržaj][00]

## 46 Sorting

Go’s slices package implements sorting for builtins and user-defined types. We’ll look at sorting for builtins first.

```go
package main

import (
    "fmt"
    "slices"
)

func main() {

    // Sorting functions are generic, and work for any ordered built-in type. For a list of ordered 
    // types, see cmp.Ordered.
    strs := []string{"c", "a", "b"}
    slices.Sort(strs)
    fmt.Println("Strings:", strs)

    // An example of sorting ints.
    ints := []int{7, 2, 4}
    slices.Sort(ints)
    fmt.Println("Ints:   ", ints)

    // We can also use the slices package to check if a slice is already in sorted order.
    s := slices.IsSorted(ints)
    fmt.Println("Sorted: ", s)
}
```

```sh
go run sorting.go
Strings: [a b c]
Ints:    [2 4 7]
Sorted:  true
```

[Sadržaj][00]

## 47 Sorting by Functions

Sometimes we’ll want to sort a collection by something other than its natural order. For example, suppose we wanted to sort strings by their length instead of alphabetically. Here’s an example of custom sorts in Go.

```go
package main

import (
    "cmp"
    "fmt"
    "slices"
)

func main() {
    
    fruits := []string{"peach", "banana", "kiwi"}

    // We implement a comparison function for string lengths. cmp.Compare is helpful for this.
    lenCmp := func(a, b string) int {
        return cmp.Compare(len(a), len(b))
    }

    // Now we can call slices.SortFunc with this custom comparison function to sort fruits by name 
    // length.
    slices.SortFunc(fruits, lenCmp)
    fmt.Println(fruits)

    // We can use the same technique to sort a slice of values that aren’t built-in types.
    type Person struct {
        name string
        age  int
    }

    people := []Person{
        Person{name: "Jax", age: 37},
        Person{name: "TJ", age: 25},
        Person{name: "Alex", age: 72},
    }

    // Sort people by age using slices.SortFunc.
    Note:
    If the Person struct is large, you may want the slice to contain *Person instead and adjust
    the sorting function accordingly. If in doubt, benchmark!

    slices.SortFunc(people,
        func(a, b Person) int {
            return cmp.Compare(a.age, b.age)
        })
    fmt.Println(people)
}
```

```sh
go run sorting-by-functions.go 
[kiwi peach banana]
[{TJ 25} {Jax 37} {Alex 72}]
```

[Sadržaj][00]

## 48 Panic

A panic typically means something went unexpectedly wrong. Mostly we use it to fail fast on errors that shouldn’t occur during normal operation, or that we aren’t prepared to handle gracefully.

```go
package main

import (
    "os"
    "path/filepath"
)

func main() {

    // We’ll use panic throughout this site to check for unexpected errors. This is the only 
    // program on the site designed to panic.
    panic("a problem")

    // A common use of panic is to abort if a function returns an error value that we don’t know 
    // how to (or want to) handle. Here’s an example of panicking if we get an unexpected error 
    // when creating a new file.
    path := filepath.Join(os.TempDir(), "file")
    _, err := os.Create(path)
    if err != nil {
        panic(err)
    }
}
```

Running this program will cause it to panic, print an error message and goroutine traces, and exit with a non-zero status.

When first panic in main fires, the program exits without reaching the rest of the code. If you’d like to see the program try to create a temp file, comment the first panic out.

```sh
go run panic.go
panic: a problem

goroutine 1 [running]:
main.main()
    /.../panic.go:12 +0x47
...
exit status 2
```

Note that unlike some languages which use exceptions for handling of many errors, in Go it is idiomatic to use error-indicating return values wherever possible.

[Sadržaj][00]

## 49 Defer

Defer is used to ensure that a function call is performed later in a program’s execution, usually for purposes of cleanup. defer is often used where e.g. ensure and finally would be used in other languages.

```go
package main

import (
    "fmt"
    "os"
    "path/filepath"
)

// Suppose we wanted to create a file, write to it, and then close when we’re done. Here’s how we 
// could do that with defer.

func main() {

    // Immediately after getting a file object with createFile, we defer the closing of that file 
    // with closeFile. This will be executed at the end of the enclosing function (main), after 
    // writeFile has finished.
    path := filepath.Join(os.TempDir(), "defer.txt")
    f := createFile(path)
    defer closeFile(f)
    writeFile(f)
}

func createFile(p string) *os.File {
    fmt.Println("creating")
    f, err := os.Create(p)
    if err != nil {
        panic(err)
    }
    return f
}

func writeFile(f *os.File) {
    fmt.Println("writing")
    fmt.Fprintln(f, "data")
}

// It’s important to check for errors when closing a file, even in a deferred function.
func closeFile(f *os.File) {
    fmt.Println("closing")
    err := f.Close()

    if err != nil {
        panic(err)
    }
}
```

Running the program confirms that the file is closed after being written.

```sh
go run defer.go
creating
writing
closing
```

[Sadržaj][00]

## 50 Recover

Go makes it possible to recover from a panic, by using the recover built-in function. A recover can stop a panic from aborting the program and let it continue with execution instead.

An example of where this can be useful: a server wouldn’t want to crash if one of the client connections exhibits a critical error. Instead, the server would want to close that connection and continue serving other clients. In fact, this is what Go’s net/http does by default for HTTP servers.

```go
package main

import "fmt"

// This function panics.

func mayPanic() {
    panic("a problem")
}

// recover must be called within a deferred function. When the enclosing function panics, the defer 
// will activate and a recover call within it will catch the panic.

func main() {

    // The return value of recover is the error raised in the call to panic.
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered. Error:\n", r)
        }
    }()

    mayPanic()

    // This code will not run, because mayPanic panics. The execution of main stops at the point of 
    // the panic and resumes in the deferred closure.
    fmt.Println("After mayPanic()")
}
```

```sh
go run recover.go
Recovered. Error:
 a problem
```

[Sadržaj][00]

[00]: 00_gbe.md
