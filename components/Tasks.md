# Tasks

## Description

The Tasks component allows game code to be broken up into individual tasks.
These tasks can be prioritized, ordered, and canceled. The manager responsible
for running the tasks can limit how much time is spent executing per frame.

## Dependencies

## Sub-Components

## Terms

- **Task State**: x
- **Task Runnable**: x
- **Task**: x
- **Task Node**: x
- **Task Group**: x
- **Task Manager**: x

## Design

#### enum TaskState
- Pending
- Running
- Canceled
- Success
- Failure

#### interface TaskRunnable< I, O >
- run( input: I ): O

#### class Task< I, O >
- input: I                          // the input to pass to the runnable
- output: O                         // the result of the runnable
- state: TaskState                  // the current state of this task
- runnable: TaskRunnable< I, O >    // the function to run
- priority: int                     // the priority of this task
- waitTime: long                    // wait this much time in the queue before auto-cancel
- queueTime: long                   // the time this task was queued on the manager
- startTime: long                   // the time when run was called on this task
- completionTime: long              // the time when this task finished execution
- safe: boolean                     // runs watching for an exception to be thrown
- error: Exception                  // an exception thrown during running
- run(): void
- reset(): void
- reset( input: I ): void
- cancel(): boolean
- isFinished(): boolean
- getDelayTime(): long
- getElapsedTime(): long

#### class TaskNode< I, O > : Task< I, O >
- next: Task< O, ? >

#### class TaskGroup : Task
- group: Task[]

#### class TaskManager
- queue: ListSorted< Task >
- running: List< Task >
- maximumExecutionTime: long
- process(): void
- queue< I, O >( task: Task< I, O > ): void
- queue< I, O >( runnable: TaskRunnable< I, O >, input: I = null, priority: int = 5 ): Task< I, O >
