# Debugging Utilities

## Description

Assist developer in finding and fixing bugs and poor performance. Handles the
"real-time" requirements a game has with the requirements to be able to inspect
code & values.

## Dependencies

- None

## Sub-Components

- Live Graphs
- Profiling
- Concurrent Access Detection
- Console
- Snapshots

## Terms

- `Stat`: Number of times an event occurs or some measurement of time, bytes, etc

## Design

### Live Graphs

#### class StatListener
- onStat( float: value, eventType: int ): void

#### class StatPoint
- total: int
- sum: double
- min: float
- max: float
- addValue( value: float ): void

#### class StatSet
- index: int
- interval: long
- points: StatPoint[]
- pointerIndex: int
- pointerTime: long
- getPoint( time: long, readonly: boolean = true ): StatPoint
- getMinimum()
- getMaximum()
- getAverage()

#### class StatDatabase : StatListener
- eventType: int
- name: string
- sets: StatSet[]
- addValue( value: float, time: long = now() ): void
- getMinimum()
- getMaximum()
- getAverage()

#### class StatGraph
- placement: Placement
- database: StatDatabase
- set: StatSet
- getLines( minPoints, maxPoints, averagePoints, verticalLines, topLine, bottomLine, leftLine, rightLine, averageLine )

### Profiling

The profiler keeps track of how much time elapses for any

#### class Profiler
- listeners: StatListener[]
- start( eventType: int ): void
- end(): void

### Concurrent Access Detection

Handles detecting whether a "resource" is being accessed illegally by multiple
threads. A resource can be read by more than one task, but only one task can
write to it.

#### class Concurrency
- eventType: int
- listener: StatListener
- allocateResource(): ConcurrentResource
- releaseResource( resource: ConcurrentResource ): void

#### class ConcurrentResource
- state: ConcurrentState
- getAccess( access: ConcurrentState ): boolean
- releaseAccess(): void

#### enum ConcurrentState
- Write
- Read
- None

**Example**
```
class MyCharacter {
  MyCharacter() {
    resource = Concurrency.allocateResource();
  }
  ~MyCharacter() {
    Concurrency.releaseResource( resource );
  }
}
class UpdateTask {
  character: MyCharacter;
  run() {
    if (character.resource.getAccess( ConcurrentState.Write )) {
      // update character
      character.resource.releaseAccess();
    }
  }
}
class DrawTask {
  character: MyCharacter;
  run() {
    if (character.resource.getAccess( ConcurrentState.Read )) {
      // draw character
      character.resource.releaseAccess();
    }
  }
}
```

### Console

The console handles displaying messages and taking commands. There can be
different communication channels (ex. one for each subsystem in the game)
and different levels of verbosity. Message spamming is prevented by simply
keeping track of the number of occurrences while the message is displayed.
Commands are sent to the console and when a match is found it is executed.
Commands are useful for changing game state live, or printing out game state.

#### ConsoleMessage
- message: string
- age: float
- occurrences: int
- verbosity: ConsoleVerbosity
- channel: int
- color: Color
- next: ConsoleMessage
- update( dt: float ): void

#### ConsoleCommand
- pattern: string                     // set time [hour]
- handler: ConsoleCommandHandler

**Pattern Tokens**
- `[hour]`: Required single word input
- `[hour=0]`: Optional single word input with a default value
- `[hour*]`: Any number word input

#### ConsoleCommandHandler
- handle( map: {}, input: string, command: ConsoleCommand, console: Console ): void

#### ConsoleCommandMatcher
- matches( input: string, command: ConsoleCommand ): boolean

#### ConsoleVerbosity
- Message = 0
- Fatal = 1
- Debug = 2
- Warn = 3

#### Console : StatListener
- channel: int
- maximumMessages: int
- lastMessage: ConsoleMessage
- maximumVerbosity: ConsoleVerbosity
- messageMap: ConsoleMessage[]
- commands: ConsoleCommand[]
- defaultCommand: ConsoleCommand
- matcher: ConsoleCommandMatcher
- setMessage( eventType: int, message: string, color: Color, verbosity: ConsoleVerbosity, channel: int ): void
- addMessageType( eventType: int ): void
- addMessage( message: string, color: Color = White, verbosity: ConsoleVerbosity = Message ): void
- getAvailableCommands( input: string ): ConsoleCommand[]
- addCommand( pattern: string, handler: ConsoleCommandHandler ): ConsoleCommand
- execute( input: string ): ConsoleCommand

### Snapshots

Snapshots take game state "pictures" and dumps them to be inspected offline.
