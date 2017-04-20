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
- Handles detecting client disconnect (as a server)
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
