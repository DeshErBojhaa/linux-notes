In linux 2.5 **Ingo Molnar** introduced `O(1)` scheduler. It has used some basic functionalities from the `Multi Level feedback
Queue Scheduler`. It's called `O(1)` scheduler because it can select/add tasks from the runqueue regardless of the number of tasks.

Although it was a good scheduler, it faces some zitter in handling realtime processes like streaming, gaming and so on. To solve thi
problem, the same guy introduces **CFS (Completely Fare Scheduler)** in linux 2.6.3

## O(1) Scheduler
Basic Properties of `O(1)` schedulers:
- 140 Priority levels, with **0 being heighest** and **139 being the lowest** priority.
- Highest priorities are usually assigned higher time slice (200ms) and Lower priorities are assigned low time slice (10ms).
- Priority levels are divided into 2 different classes.0 to 99 Priority levels are reserved for real time class
and 100 to 139 priority levels are called time shared class.
- Dafault prairity of a user level task is 120.
- User level priorities can be updated with a `nice-value` system call that can change the priority of a task by a value from
-20 to +19.
- Feedback:

   The idea is if a task spends more time in sleeping (waiting for I/O or or user input or other resources) that means its a more interactive 
   tak (**I/O bound**). And the scheduler will assigne it a bigger time slice because more lilely it will not use the total slice assigned to it
   and will go back to waiting state because it requirs freequent feedback (input from user maybe).
   
   On the other hand if a task spends less time in sleeping, that means its a **CPU bound** task. It does not rely on I/O that
   much. And it's more likely to spend all the time assigned to it. And thus the scheduler assignes a less time slice to it.
   
   Now, when a task spends more time in sleeping, it's priority is boosted (priority-5), vice versa, if a task spends less
   time in sleeping, it's priority is lowered (`priority = priority + 5`).

- Runqueue:
   The runqueue is divided into 2 array of tasks. One is for **active taskes** and one is for **expired tasks**.
   

![O(1)scheduler](https://user-images.githubusercontent.com/46394832/76158859-a315b280-6144-11ea-8a10-06ab0e9fe007.jpg)
