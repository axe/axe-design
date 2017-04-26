# Audio

## Description

The Audio component loads and streams audio files in game. Audio loaded into a
game can sound different based on the distance of the sound source to the
listener, the obstacles between the sound and the listener, and the surfaces
or environment around the source. The Audio component accounts for limitations
in hardware by prioritizing louder and more important sound effects.

## Dependencies

- [Core](Core.md)
- [Math](Math.md)

## Sub-Components

- None

## Terms

- **AudioState**:
- **AudioModel**:
- **AudioData**:
- **AudioDataBuffer**:
- **AudioDataStream**:
- **AudioDataSource**:
- **AudioBank**:
- **Audio**:
- **AudioSpatial**:
- **AudioListener**:
- **AudioObstruction**:
- **AudioEnvironment**:
- **AudioEffect**:
- **AudioEngine**:

## Design

#### enum AudioState
- Stopped
- Playing
- Muted
- Paused

#### enum AudioModel
- None
- Linear
- LinearClamped
- Inverse
- InverseClamped
- Exponential
- ExponentialClamped

#### interface AudioData : AudioDataSource
- maximumInstances: int
- load(): boolean
- delete(): boolean
- isLoaded(): boolean
- hasData(): boolean
- isStreaming(): boolean
- update( audio: Audio ): boolean
- clear( audio: Audio ): void
- attach( audio: Audio ): void
- getFormat(): int
- getSampleRate(): int
- getDuration(): float

#### class AudioDataBuffer : AudioData
- data: byte[]

#### class AudioDataStream : AudioData
- source: stream
- buffer: byte[]

#### interface AudioDataSource
- getData( flags: long ): AudioData

#### class AudioBank : AudioDataSource, Distribution< AudioData >

#### interface Audio : HasFlags, AudioFactory
- state: AudioState
- data: AudioData
- effect: AudioEffect
- offset: float
- length: float
- volume: float
- pitch: float
- pan: float
- looping: boolean
- relative: boolean
- play(): boolean
- pause(): boolean
- stop(): boolean
- mute( muted: boolean ): void
- sync(): boolean
- isSpatial(): boolean
- isPlaying(): boolean
- isPaused(): boolean
- isStopped(): boolean
- getTime(): float
- setTime( time: float ): void
- fadeOut( duration: float ): void
- fadeIn( duration: float ): void
- update( time: GameTime ): void

#### interface AudioSpatial< V > : Audio
- position: V
- velocity: V
- minDistance: float
- maxDistance: float
- minVolume: float
- maxVolume: float
- direction: V
- innerAngle: float
- outerAngle: float
- outerGain: float
- fixed: boolean

#### interface AudioListener< V >
- position: V
- velocity: V
- forward: V
- up: V
- volume: float

#### interface AudioObstructions< V >
- getObstruction( listener: AudioListener< V >, audio: AudioSpatial< V > ): float

#### interface AudioEnvironment< V >
- getEnvironmentEffect( audio: AudioSpatial< V > ): AudioEffect

#### interface AudioEffect
- apply( audio: Audio ): void

#### interface AudioEngine< V >
- listener: AudioListener< V >
- calculator: Calculator< V >
- obstructions: AudioObstructions< V >
- environment: AudioEnvironment< V >
- compare: Compare< Audio >
- audio: ListSorted< Audio >
- isInitialized(): boolean
- initialize(): void
- destroy(): void
- setModel( model: AudioModel ): void
- setDopplerFactory( factor: float ): void
- setSpeedOfSound( speed: float ): void
- newAudio( data: AudioDataFactory, flags: long = 0 ): Audio
- newAudioSpatial( data: AudioDataFactory, flags: long = 0 ): AudioSpatial< V >
- setStates( state: AudioState, flags: long, match: Match ): int
- fadeIn( duration: float, flags: long, match: Match ): int
- fadeOut( duration: float, flags: long, match: Match ): int
- forEach( consumer: Consumer< Audio > ): int
- forEachFlagged( flags: long, match: Match, consumer: Consumer< Audio > ): int
- forEachState( state: AudioState, consumer: Consumer< Audio > ): int
- getMaximumActive(): int
- update( time: GameTime ): void
