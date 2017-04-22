# Networking

## Description

The networking component handles the communication between a game client and one
or more servers. Some servers are used to manage the player's account, some find
a game for the player, and some run the game logic.

This component is compromised of 4 layers:

1. Transportation
2. Protocol
3. Messages
4. Game

#### 1. Transportation

- Handles connecting and disconnecting from a server (as a client)
- Handles accepting new clients (as a server)
- Handles detecting connection disconnect
- Sends data as packets through a connection
- Receives data as packets through a connection

#### 2. Protocol

- Validates packets
- Takes messages off of the message layer queue and writes them to a buffer
- Reads messages from a buffer and adds them to a processing queue
- Handles large messages that are broken up
- Implements as much of the messaging features as possible

#### 3. Messages

- Keeps a prioritized queue of messages to send through a connection
- Handles messages which failed to send and may add them to the queue to retry
- Handles dispatching processed (read) messages
  - Validates message by comparing message state to current connection state

#### 4. Game

- Uses messages to synchronize actors between client and server
  - Server handles how much actor state to communicate (all, just position, just sound, none)
  - Server determines which actors need synchronized each frame (frequency, relevancy)
- Provides utilities for communicating between client and server via remote procedure calls

### References

1. https://docs.unrealengine.com/udk/Three/NetworkingOverview.html#Relevancy
2. https://developer.valvesoftware.com/wiki/Source_Multiplayer_Networking
3. https://0fps.net/2014/02/10/replication-in-networked-games-overview-part-1/
4. https://0fps.net/2014/02/17/replication-in-networked-games-latency-part-2/
5. https://0fps.net/2014/02/26/replication-in-networked-games-spacetime-consistency-part-3/
6. https://0fps.net/2014/03/09/replication-in-network-games-bandwidth-part-4/
7. http://www.gabrielgambetta.com/fast_paced_multiplayer.html
8. http://gafferongames.com/networking-for-game-programmers/

## Dependencies

- [Core](Core.md)
- [Debugging Utilities](DebuggingUtilities.md)

#### Game Layer
- [Easing](Easing.md)
- [Math](Math.md)
- [Paths](Paths.md)

## Sub-Components

## Terms

- x

## Design

#### class DataOptions
- fixedMin: number
- fixedMax: number
- fixedBytes: number
- useAngles: boolean // 2d normal = angle, 3d normal = yaw & pitch
- forNormal( bytes: number, angles: boolean ): void
- forPosition( bytes: number, min: number, max: number ): void

#### class DataBuffer
- setOptions( options: DataOptions ); void
- sizeBooleans( bools: boolean[] ): number
- writeBooleans( bools: boolean[] ): number
- readBooleans( out: boolean[], count: number ): void
- sizeFloat( value: float ): number
- writeFloat( value: float ): number
- readFloat(): number
- sizeInt( value: number ): number
- writeInt( value: number ): number
- readInt(): number
- sizeNormal2( x: number, y: number ): number
- writeNormal2( x: number, y: number ): number
- readNormal2( out: number[] ): number[]
- sizeNormal3( x: number, y: number, z: number ): number
- writeNormal3( x: number, y: number, z: number ): number
- readNormal3( out: number[] ): number[]
- getSize(): number
- getBytes( out: number[] ): number

#### interface DataTransformer< T >
- sizeof( data: T ): number
- write( data: T, buffer: DataBuffer ): number
- read( buffer: DataBuffer ): T

#### interface Protocol
- magicNumber: number
- packetSize: number
- newClient( host: string, port: number ): Client
- newServer( port: number ): Server
- setChannel( id: number, reliable: boolean, ordered: boolean ): Channel
- getChannel( id: number ): Channel

#### interface Client
- getState(): Flags
- init(): void
- isInitialized(): boolean
- read(): void
- update(): void
- isReadyToSend(): boolean
- queue( message: Message )
- addFactory( factory: MessageFactory ): void
- send(): void
- sendAllNow(): void
- close(): void
- isClosed(): boolean
- getServer(): Server
- getProtocol(): Protocol
- getAddress(): Address
- getOutbound(): PriorityQueue< Message >
- getInbound(): Queue< Message >
- getUpdateRate(): long
- setUpdateRate( millis: number ): void
- getLastUpdateTime(): long
- getMessagesSent(): int
- getPacketsRead(): int
- getPacketIndex(): int
- getLastPacketSize(): int
- getReadNanos(): long
- getUpdateNanos(): long
- getWriteNanos(): long
- getSendNanos(): long
- getSendIndex(): int

#### interface Server
- getState(): Flags

#### class Channel
- getId(): number
- getReliable(): boolean
- getOrdered(): boolean
- getLastPacket(): number

#### class MessageType
- getId(): number
- getPriority(): number
- getChannel(): Channel
- getRetryCount(): number
- getReadStates(): FlagsMatch
- getWriteStates(): FlagsMatch

#### Message
- getType(): MessageType
- getTries(): number
- getPriority(): number
- write( out: buffer ): boolean
- read( in: buffer ): boolean
- size(): number

#### MessageFactory : Factory< Message >
- getCreateStates(): FlagsMatch
- create(): Message

#### Services
- newService< T >( remoteInterface: T ): Service< T >
- addInterface< T >( remoteInterface: T ): void
- addListener< T >( listener: T ): void

#### Service< T >

### 4. Game

What does the developer need to do to run a networked game?
- Configure simulation properties
- Create a component producer for each type of object that may be communicated between server and client. This allows the networking system to get and set values to a subject.
- Define the actor types and their attributes.
  - Attributes have a calculator, level of detail information, interpolation information, and serialization information
- Create an applier which takes the state of a network actor and applies any additional logic to a subject
- Define how relevant an actor is to a player
- Handle actor creation and destruction events
- Define which inputs are sent for a player to the server (mouse movement, directional keys, shooting)
- Define how inputs affect the actors for a player (used in client-side prediction and server simulation)

#### ComponentProducer< S >
- getComponent< T >( subject: S, component: number ): T
- setComponent< T >( subject: S, component: number, value: T ): void

#### NetworkStateMessage : Message
- time: number
- actors: NetworkActorState[]

**Format**
[ remote time ]
[ actor count ]
[ actor N id ]
[ actor N message type ]
[ actor N component bits ] - only for partial updates
[ actor N creation flags ] - only for creation events (type, player, alwaysRelevant)
[ actor N component data ] - only for creation, full updates, and partial updates

**Notes**
- Message types: creation, full update, partial updates, destruction
- When actor id does not exist locally and all component bits sent - its a creation event
- When actor id exists locally and component bits is empty - its a destruction event

#### NetworkActorState
- actor: NetworkActor
- messageType: number
- type: number
- player: number
- alwaysRelevant: boolean
- componentBits: number
- componentData: number[]

#### NetworkSimulation
- player: NetworkPlayer
- otherPlayers: NetworkPlayer[]
- latencyCompensation: number         // set remote actors X ms (ex: 100) behind the latest state in the event we miss state updates
- processors: NetworkProcessorInstance[]
- input: NetworkInputs

#### NetworkPlayer
- id: number
- currentActor: NetworkActor
- actors: NetworkActor[]
- relevantActors: NetworkActorRelevance[]
- relevancies: NetworkActorRelevancy[]

#### NetworkActorRelevancy
- update( player: NetworkPlayer, relevance: NetworkActorRelevance, time: number, elapsed: GameTime ): void

**Implementations**
- Distance { componentPosition, maxDistance, maxDistancePriority, maxDistanceLOD, minDistance, minDistancePriority, minDistanceLOD }
- Field Of View { componentLookAt, fovAngle, priority, lod }
- Coherency { seconds }
- Running Average { samples }

#### NetworkActorRelevance
- actor: NetworkActor
- priority: number
- levelOfDetail: number
- reset(): void
- addLevelOfDetail( lod: number ): void // ensures the level of detail is the minimum necessary
- setPriority( priority: number ): void // ensures the priority is the maximum necessary
- addPriority( priority: number ): void // adds priority to the relevance

#### NetworkProcessor< S >
- update( subject: S, inputs: NetworkInputs, time: GameTime ): void

#### NetworkProcessorInstance< S >
- subject: S
- processor: NetworkProcessor< S >

#### NetworkActorType< S >
- id: number                          // an id that uniquely identifies this type
- priority: number                    // the default send priority for actors
- attributes: NetworkAttributeType[]  // the descriptions of each of the attributes
- components: ComponentProducer< S >  // takes an attribute from a subject instance
- factory: Factory< S >               // creates a subject instance
- applier: NetworkActorApplier< S >   // applies the network actor state at a given time to a subject. some properties of an actor require events on change (like animation state)

#### NetworkActor< S >
- id: number                          // and id that uniquely identifies this actor
- priority: number                    // the send priority for the actor
- owner: NetworkPlayer                // which player created this actor
- author: boolean                     // if i'm the author, I need to send the state
- simulating: boolean                 // whether the attribute history is being applied to the subject
- remote: boolean                     // whether this actor is processed remotely or locally
- alwaysRelevant: boolean             // if this actor's state should always be sent
- type: NetworkActorType< S >         // the actor type
- attributes: NetworkAttribute[]      // the attributes
- subject: S                          // the subject being synchronized

#### NetworkActorApplier< S >
- apply( subject: S, actor: NetworkActor< S >, time: number ): void

#### NetworkAttributeType< T >
- index: number                       // the index of the attribute in the actor
- component: number                   // the component index on the subject
- maximumLevelOfDetail: number        // the level of detail this attribute is on (0 = all details, 1 = fewer, 2 = fewest, etc)
- allowableDistance: number           // the maximum allowed distance a value can be from the authoritative source before it needs to be harshly corrected
- calculator: Calculator< T >         // the calculator which is passed to path calculations
- pathGenerator: PathGenerator< T >   // generate a path of values based on the current values
- maxExtrapolationTime: number        // the maximum amount of time we go past the latest value in an attempt to guess the unknown future position due to latency
- extrapolationDamping: number        // a value which discourages extrapolation over time (a slowing down of extrapolation over time)
- dataOptions: DataOptions            // the options for reading & writing this type
- dataTransformer: DataTransformer< T > // writes & reads the attribute to a data buffer

#### NetworkAttribute< T >
- path: Path<T>                       // the path generated based on the values available
- history: AttributeHistory< T >      // the history of values for this attribute

#### NetworkInput< T >
- index: number                       // the input
- value: T                            // the input value
- dataOptions: DataOptions            // the options for reading & writing this input
- dataTransformer: DataTransformer< T > // writes & reads the network input to a data buffer
- history: AttributeHistory< T >      // the history of inputs

#### NetworkInputs
- inputs: NetworkInput[]

**Format**
[ input bits ]
[ input N data ]

#### AttributeHistory< T >
- calculator: Calculator< T >
- times: number[]
- values: T[]
- size: number
- current: number
- setValue( time: number, value: T ): void
- getValue( time: number ): T
- hasValue( time: number ): boolean
- getValueAtIndex( index: number ): T
- getIndexBeforeTime( time: number ): number
- getTimeMinimum(): number
- getTimeMaximum(): number
- getValueCount(): number
