# Advanced Programming Tutorial 10

### Understanding How it Works

![Image](https://github.com/user-attachments/assets/4c918548-bab5-4ef7-9669-d6ac42d0f4d6)

This output reveals a fundamental aspect of asynchronous execution in Rust. The message "hey hey" appears before "howdy!" even though the spawn call happens first in the code. This is because an async task in Rust does not block the main thread—it is scheduled to run by the executor, but not executed immediately. The spawner.spawn(...) merely queues the task, and the main thread continues to execute, printing "hey hey" straight away. Meanwhile, the executor begins running the scheduled task, which then prints "howdy!" and, after awaiting a 2-second delay via TimerFuture, finally prints "done!".

This behavior demonstrates that async functions in Rust run concurrently and do not interfere with the main synchronous flow. It highlights how task scheduling and execution are decoupled, and why logs from async tasks may appear later despite being spawned earlier in the code.

### Multiple Spawn and Removing Drop

#### With Drop Spawner

![Image](https://github.com/user-attachments/assets/80ab03f1-4760-4c8c-a09e-6455ae5ca02d)

#### Removed Drop Spawner

![Image](https://github.com/user-attachments/assets/a5b96c97-6863-4aa2-abc8-b98648af0b7e)

In this experiment, I observed the behavior of spawning multiple asynchronous tasks and the impact of including or omitting the drop(spawner) statement. I created three separate async tasks using spawner.spawn(...), each printing a message before and after waiting on a two-second timer (TimerFuture). Two screenshots were taken to compare both scenarios. In the first image, where drop(spawner) is present, the executor correctly completes all tasks, and the output shows all "howdy!" and "done!" messages printed in full. In contrast, the second image shows the result after removing drop(spawner)—the "done!" messages are not always completed, or the program hangs, because the executor continues waiting for new tasks indefinitely. This highlights the importance of properly signaling the executor when no further tasks are expected.

**What is the effect of spawning?** <br>

The effect of spawning is that it schedules a future (async task) to be run by the executor. However, calling spawn() does not immediately execute the task; instead, it places the task into a queue managed by the executor. This allows the program to remain responsive and efficient, especially when handling asynchronous workloads that depend on timers, I/O, or other delayed computations.

**What is the spawner for, what is the executor for, what is the drop for?** <br>

The spawner is responsible for creating and submitting tasks into the executor's queue. The executor then picks up these tasks and runs them to completion by polling their futures. The drop(spawner) call is essential because it tells the executor that no more tasks will be added. Without this, the executor will continue waiting for additional tasks and may never exit, even if all scheduled tasks are already done.

**What is the correlation of all of that?** <br>

The correlation among these components is fundamental to building a working async system in Rust. The spawner schedules the work, the executor performs it, and the drop(spawner) ensures the executor knows when to stop. Together, they form a minimal asynchronous runtime. If any part is missing or misused—such as forgetting to drop the spawner—the entire system may behave unexpectedly, demonstrating how important synchronization and lifecycle management are in async programming.