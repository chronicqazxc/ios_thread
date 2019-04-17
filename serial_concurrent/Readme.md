
# Explain Serial and Concurrent by GCD API
## Dispatch Queue:
  User just add their tasks into the proper queue. Queue execute tasks according to the sequence of addition. (FIFO)
## Here are two types of queue.
1. Serial queue:
  Jobs will be executed until prior job has complete.
  Every jobs added to the serial queue will be on the same thread, but multiple serial queue equal simultaneously process.
2. Concurrent queue:
  Jobs will not pending by prior job and will execute simultaneously.
  Every jobs added to the concurrent queue will be on different threads.

* `async` was used in the demo in order to avoid block the calling thread.

```swift
extension DispatchQueue {
    static let experimentConcurrentQueue = DispatchQueue(label: "com.wayne.experimentConcurrentQueue", qos: .utility, attributes: .concurrent)
    static let experimentSerialQueue = DispatchQueue(label: "com.wayne.experimentDerialQueue", qos: .utility)

    func runExperiment() {
        async {
            for i in 0...10 {
                print("TaskA: \(String(format: "%02d", i)) - \(Thread.current)")
                Thread.sleep(forTimeInterval: 1)
            }
        }

        async {
            for i in 0...10 {
                print("TaskB: \(String(format: "%02d", i)) - \(Thread.current)")
                Thread.sleep(forTimeInterval: 0.1)
            }
        }
    }
}
```

```swift
// Serial Queue demo 
DispatchQueue.experimentSerialQueue.runExperiment()
// Concurrent Queue demo 
DispatchQueue.experimentConcurrentQueue.runExperiment()
```

# Experiment Result
## SerialQueue
```
TaskA: 00 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 01 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 02 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 03 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 04 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 05 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 06 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 07 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 08 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 09 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskA: 10 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 00 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 01 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 02 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 03 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 04 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 05 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 06 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 07 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 08 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 09 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
TaskB: 10 - <NSThread: 0x60000088dd80>{number = 4, name = (null)}
```
## Concurrent Queue
```
TaskA: 00 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskB: 00 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 01 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 02 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 03 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 04 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 05 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 06 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 07 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskA: 01 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskB: 08 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 09 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskB: 10 - <NSThread: 0x600003b282c0>{number = 5, name = (null)}
TaskA: 02 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskA: 03 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskA: 04 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskA: 05 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskA: 06 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskA: 07 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskA: 08 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskA: 09 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
TaskA: 10 - <NSThread: 0x600003b1dfc0>{number = 4, name = (null)}
```
