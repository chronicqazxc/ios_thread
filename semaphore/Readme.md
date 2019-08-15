# Semaphore
![](https://img.shields.io/badge/Swift-v4.2-blue)

Semaphore is a mechanism which adopt signal with count. Thread freezed when count equal zero, the count will be subtract one and the thread will be executed if count greater or equal one. The parameter passed to the Semaphore initial method, indicate how many threads can be process in the moment. Semaphore also can be used in Global queue.

We can use semaphore to make sure only one thread can access shared resources.

* Don't use semaphore in MainThread otherwise the UI will freeze while waiting.

## Snippet
```swift
extension DispatchQueue {
    static let experimentConcurrentQueue = DispatchQueue(label: "com.wayne.experimentConcurrentQueue", qos: .utility, attributes: .concurrent)
    static let experimentSerialQueue = DispatchQueue(label: "com.wayne.experimentDerialQueue", qos: .utility)
}

let amoutOfThreadAllowedToAccess = 1
let semaphore = DispatchSemaphore(value: amoutOfThreadAllowedToAccess)
func signInProcess(_ stuffId: String, defer: TimeInterval, _ complete: ()->Void) {
    // count -1; if count <= 0 then wait else execute
    semaphore.wait()
    for i in 1...10 {
        Thread.sleep(forTimeInterval: `defer`)
        let content = String(format: "\(stuffId) sing in %02d%% on \(Thread.current).", i*10)
        print(content)
    }
    complete()
    // count+1
    semaphore.signal()
}

DispatchQueue.experimentConcurrentQueue.async {
    signInProcess("Stuff A", defer: 0.1) {
        print("Stuff A sing in complete.")
    }
}

DispatchQueue.experimentConcurrentQueue.async {
    signInProcess("Stuff B", defer: 0.2) {
        print("Stuff B sing in complete.")
    }
}

DispatchQueue.experimentConcurrentQueue.async {
    signInProcess("Stuff C", defer: 0.3) {
        print("Stuff C sing in complete.")
    }
}
```

## Experiement Result
```
Stuff B sing in 10% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 20% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 30% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 40% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 50% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 60% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 70% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 80% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 90% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in 100% on <NSThread: 0x600000bb6ec0>{number = 5, name = (null)}.
Stuff B sing in complete.
Stuff C sing in 10% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 20% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 30% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 40% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 50% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 60% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 70% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 80% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 90% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in 100% on <NSThread: 0x600000ba2f00>{number = 6, name = (null)}.
Stuff C sing in complete.
Stuff A sing in 10% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 20% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 30% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 40% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 50% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 60% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 70% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 80% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 90% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in 100% on <NSThread: 0x600000b9fcc0>{number = 7, name = (null)}.
Stuff A sing in complete.
```

Author: [Wayne Hsiao](mailto:chronicqazxc@gmail.com)
