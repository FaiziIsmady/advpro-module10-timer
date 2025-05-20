# Advanced Programming Tutorial 10

### Understanding How it Works

![Image](https://github.com/user-attachments/assets/4c918548-bab5-4ef7-9669-d6ac42d0f4d6)

This output reveals a fundamental aspect of asynchronous execution in Rust. The message "hey hey" appears before "howdy!" even though the spawn call happens first in the code. This is because an async task in Rust does not block the main thread—it is scheduled to run by the executor, but not executed immediately. The spawner.spawn(...) merely queues the task, and the main thread continues to execute, printing "hey hey" straight away. Meanwhile, the executor begins running the scheduled task, which then prints "howdy!" and, after awaiting a 2-second delay via TimerFuture, finally prints "done!".

This behavior demonstrates that async functions in Rust run concurrently and do not interfere with the main synchronous flow. It highlights how task scheduling and execution are decoupled, and why logs from async tasks may appear later despite being spawned earlier in the code.