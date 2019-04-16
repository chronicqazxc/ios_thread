# Barrier

Sometimes we will process database process or file concurrently for efficient, it is save to use concurrent for read operate but not save for write operate. Barrier in GCD can help us to resolve the data race condition issues.
`barrier` will add it's task to the queue until all tasks on the concurrent queue was completed, the `barrier` task is executed exclusively on the queue. The queue operate will become to normal after `barrier` task completed.

* Global queue in GCD can not be blocked with a barrier.

## Snippet
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

func longRun(_ taskId: String, defer: TimeInterval, _ complete: ()->Void) {
    for i in 1...10 {
        Thread.sleep(forTimeInterval: `defer`)
        print("\(taskId)\(i) on \(Thread.current)")
    }
    complete()
}

//// Multithread
// 1. Goup
DispatchQueue.experimentConcurrentQueue.async {
    longRun("ReadA", defer: 0.3) {

    }
}
DispatchQueue.experimentConcurrentQueue.async {
    longRun("ReadB", defer: 0.1) {

    }
}
DispatchQueue.experimentConcurrentQueue.async(flags: .barrier) {
    longRun("WriteC", defer: 0.3) {

    }
}
DispatchQueue.experimentConcurrentQueue.async {
    longRun("ReadD", defer: 0.3) {

    }
}
DispatchQueue.experimentConcurrentQueue.async {
    longRun("ReadE", defer: 0.3) {

    }
}
```

## Experiment Result
```
ReadB1 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadB2 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadA1 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadB3 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadB4 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadA2 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadB5 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadB6 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadA3 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadB7 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadB8 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadA4 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadB9 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadB10 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadA5 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadA6 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadA7 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadA8 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadA9 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadA10 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC1 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC2 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC3 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC4 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC5 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC6 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC7 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC8 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC9 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
WriteC10 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadD1 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE1 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD2 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE2 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD3 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE3 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD4 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE4 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD5 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE5 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadE6 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD6 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE7 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD7 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE8 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD8 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE9 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD9 on <NSThread: 0x6000002da500>{number = 6, name = (null)}
ReadE10 on <NSThread: 0x6000002dd3c0>{number = 5, name = (null)}
ReadD10 on <NSThread: 0x6000002da500>{number = 6, name = (null)}

```
