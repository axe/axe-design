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

- `Debug Event`: An event that occurs or a measurement that's taken that is
  pertinent to debugging performance and game state issues. Various components
  in a game can produce and consume debug events. A debug value as a floating
  precision number is the only metadata associated with an event. Debug events
  are referenced by an integer identifier. Debug events can also be hierarchal,
  for example a profiler can track how much time it takes to update the game
  and it may also track how much time each component takes to run.
  - Examples
    - When a conflict in concurrent access occurs in a multithreaded game.
    - Networking statistics like packet size, round trip time, dropped packets, etc.
    - How much time it takes to run physics, AI, spatial database querying, etc.
  - Producers
    - Profiler
    - Concurrent Access Detection
    - Networking
  - Consumers
    - Statistical Database / Graph
    - Console
    - Logger
    - Snapshots
- `Statistics Database`: A round robin database that tracks debug events over
  various time intervals. These databases are fixed size and can track events
  over the past second, minute, to hour.
- `Console`: A list of recent messages and a control which takes commands
  entered by the player. Debug events can be linked to messages to display on
  the console.
- `Profiler`: Triggers debug events based on how much time it takes to execute a  
  segment of code.
- `Snapshots`: A summary of the state of a game taken at a single frame or over
  multiple frames.


## Design

#### function DebugEventListener
- ( float: debugValue, debugEvent: int, parentDebugEvent: int ): void

#### function DebugEventListenerChain : DebugEventListener
- first: DebugEventListener
- second: DebugEventListener

#### class Debug
- events: DebugEvent[]
- newDebugEvent( name: string ): int
- getDebugEvent( id: int ): DebugEvent

#### class DebugEvent
- id: int
- name: string
- parent: DebugEvent
- children: DebugEvent
- sibling: DebugEvent
- getChildren( consumer: Consumer< DebugEvent > ): void

### Live Graphs

#### class StatPoint
- total: int
- sum: double
- min: float
- max: float
- addValue( value: float ): void

#### class StatSet
- index: int
- description: string
- interval: long
- points: StatPoint[]
- pointerIndex: int
- pointerTime: long
- getPoint( time: long, readonly: boolean = true ): StatPoint
- getMinimum()
- getMaximum()
- getAverage()

#### class StatDatabase : DebugEventListener
- debugEvent: int
- name: string
- sets: StatSet[]
- time: StatTime
- live: boolean
- alwaysLive: boolean
- addValue( value: float, time: long ): void
- isActive(): boolean
- getMinimum()
- getMaximum()
- getAverage()

#### interface StatTime
- getCurrentTime(): long

#### class StatGraph
- placement: Placement
- database: StatDatabase
- set: StatSet
- getLines( minPoints, maxPoints, averagePoints, verticalLines, topLine, bottomLine, leftLine, rightLine, averageLine )
- getDebugEvent(): DebugEvent

#### class Stats : DebugEventListener
- time: StatTime
- databases: StatDatabase[]
- newDatabase( debugEvent: int, perSecond: float, lastMinute: boolean, lastHour: boolean ): int
- newDatabase( eventName: string, perSecond: float, lastMinute: boolean, lastHour: boolean ): int
- getDatabase( debugEvent: int )


**Example**
```
// Initialization of game
int CHARACTER_UPDATE = Stats.newDatabase("character update", 30, true, true);
int CHARACTER_PHYSICS = Stats.newDatabase("character physics", 30, true, true);
int CHARACTER_ANIMATION = Stats.newDatabase("character animation", 30, true, true);

Profiler profiler = new Profiler();
profiler.listener = Stats;

// During game
profiler.start( CHARACTER_UPDATE );
  profiler.start( CHARACTER_PHYSICS );
    // character physics logic
  profiler.end();
  profiler.start( CHARACTER_ANIMATION );
    // character animation logic
  profiler.end();
profiler.end();

// With a StatsGraph on screen you can see character update times, and also the
// options to view sub events physics and animation.
```

### Profiling

The profiler keeps track of how much time elapses over the execution of a
segment of code. Profiling can be nested - so within a segment of code you have
various other segments being profiled individually.

#### class Profiler
- listener: DebugEventListener
- start( debugEvent: int ): void
- end(): void

### Concurrent Access Detection

Handles detecting whether a "resource" is being accessed illegally by multiple
threads. A resource can be read by more than one task, but only one task can
write to it.

#### class Concurrency
- debugEvent: int
- listener: DebugEventListener
- allocateResource( debugEvent: int = debugEvent ): ConcurrentResource
- releaseResource( resource: ConcurrentResource ): void

#### class ConcurrentResource
- debugEvent: int
- state: ConcurrentState
- concurrency: Concurrency
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

#### class ConsoleMessage
- message: string
- age: float
- occurrences: int
- verbosity: ConsoleVerbosity
- channel: int
- color: Color
- displayed: boolean
- next: ConsoleMessage
- update( dt: float ): void

#### class ConsoleCommand
- pattern: string                     // set time [hour]
- handler: ConsoleCommandHandler

**Pattern Tokens**
- `[hour]`: Required single word input
- `[hour=0]`: Optional single word input with a default value
- `[hour*]`: Any number word input

#### function ConsoleCommandHandler
- ( map: {}, input: string, command: ConsoleCommand, console: Console ): void

#### function ConsoleCommandMatcher
- ( input: string, command: ConsoleCommand ): boolean

#### enum ConsoleVerbosity
- Message = 0
- Fatal = 1
- Debug = 2
- Warn = 3

#### class Console : DebugEventListener
- visible: boolean
- channel: int
- maximumMessages: int
- lastMessage: ConsoleMessage
- maximumVerbosity: ConsoleVerbosity
- messageMap: ConsoleMessage[]
- commands: ConsoleCommand[]
- defaultCommand: ConsoleCommand
- matcher: ConsoleCommandMatcher
- setDebugMessage( debugEvent: int, message: string, color: Color, verbosity: ConsoleVerbosity, channel: int ): void
- addDebugMessage( debugEvent: int ): void
- addMessage( message: string, color: Color = White, verbosity: ConsoleVerbosity = Message ): void
- getAvailableCommands( input: string ): ConsoleCommand[]
- addCommand( pattern: string, handler: ConsoleCommandHandler ): ConsoleCommand
- execute( input: string ): ConsoleCommand

### Snapshots

Snapshots take game state pictures or videos and dumps them to be inspected offline.

#### function SnapshotTaker< T >
- ( snapshot: Snapshot )

#### enum SnapshotAggregate
- None = 0
- Min = 1
- Max = 2
- Average = 4
- Total = 8
- First = 16
- Last = 32
- List = 64
- Count = 128
- Group = 256

#### class Snapshot : DebugEventListener
- values: SnapshotValue[]
- begin( name: string ): void
- addNumber( name: string, value: float, aggregates: int ): void
- addString( name: string, value: string, aggregates: int ): void
- end(): void

#### class SnapshotValue
- path: string
- values: float[]

#### class Snapshots
- takers: SnapshotTaker
- snapshot: Snapshot
- snap(): void
- begin(): void
- end(): void
- update(): void
