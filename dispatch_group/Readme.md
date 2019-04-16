# DispatchGroup
Image you have two long run service tasks, after all tasks done you will render screen based on the results.
You can call the services perspectively but it is not efficiently, so we can use `DispatchGroup` to call multiple service call concurrently.
At first you will create a `dispatchGroup` and add `dispatchGroup.enter()` before each service call and call `dispatchGroup.leave()` after each service call completed.
You will add `dispatchGroup.notify` on mean thread to render UI, beside `notify` there are several options you can choose for further control.
`wait()` will block further processes to current thread.
`wait(timeout: .now() + x)` will block further process to current thread until `x` seconds.

# Snippet
```swift
extension DispatchQueue {
    static let experimentConcurrentQueue = DispatchQueue(label: "com.wayne.experimentConcurrentQueue", qos: .utility, attributes: .concurrent)
    static let experimentSerialQueue = DispatchQueue(label: "com.wayne.experimentDerialQueue", qos: .utility)
}

func longRun(_ taskId: String, defer: TimeInterval, _ complete: ()->Void) {
    for i in 1...10 {
        Thread.sleep(forTimeInterval: `defer`)
        print("\(taskId): \(String(format: "%02d", i)) - \(Thread.current)")
    }
    complete()
}

//// Multithread
// 1. Goup
let dispatchGroup = DispatchGroup()
dispatchGroup.enter()
DispatchQueue.experimentSerialQueue.async(group: dispatchGroup) {
    longRun("A", defer: 0.3) {
        dispatchGroup.leave()
    }
}
dispatchGroup.enter()
DispatchQueue.experimentConcurrentQueue.async(group: dispatchGroup) {
    longRun("B", defer: 0.1) {
        dispatchGroup.leave()
    }
}

dispatchGroup.notify(queue: .main) {
    print("Both tasks was completed - \(Thread.current)")
}

// This will block the current thread
dispatchGroup.wait(timeout: .now()+0.5)
print("Print after 0.5 seconds - \(Thread.current)")
dispatchGroup.wait()
print("Will be executed after all enters were leaved - \(Thread.current)")
```

# Experiment Result
```
B: 01 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
B: 02 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
A: 01 - <NSThread: 0x600002467080>{number = 6, name = (null)}
Print after 0.5 seconds - <NSThread: 0x60000247e840>{number = 1, name = main}
B: 03 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
B: 04 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
A: 02 - <NSThread: 0x600002467080>{number = 6, name = (null)}
B: 05 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
B: 06 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
A: 03 - <NSThread: 0x600002467080>{number = 6, name = (null)}
B: 07 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
B: 08 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
A: 04 - <NSThread: 0x600002467080>{number = 6, name = (null)}
B: 09 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
B: 10 - <NSThread: 0x60000246a6c0>{number = 5, name = (null)}
A: 05 - <NSThread: 0x600002467080>{number = 6, name = (null)}
A: 06 - <NSThread: 0x600002467080>{number = 6, name = (null)}
A: 07 - <NSThread: 0x600002467080>{number = 6, name = (null)}
A: 08 - <NSThread: 0x600002467080>{number = 6, name = (null)}
A: 09 - <NSThread: 0x600002467080>{number = 6, name = (null)}
A: 10 - <NSThread: 0x600002467080>{number = 6, name = (null)}
Will be executed after all enters were leaved - <NSThread: 0x60000247e840>{number = 1, name = main}
Both tasks was completed - <NSThread: 0x60000247e840>{number = 1, name = main}
```
