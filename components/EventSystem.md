# Event System

## Description

This component offers a simple and decoupled way for components to communicate. Events are defined given a function definition and a name and an ID is generated. Any number of subscribers can listen to events. Events can be queued and handled in batches to keep subscribers being notified at a controlled time.

## Dependencies

- None

## Sub-Components

- None

## Terms

- **Event**: A named function to dispatch to all subscribers
- **Event Listener**: A prioritized subscriber
- **Event Listener List**: A prioritized list of listeners for a specific event
- **Event System**: The structure that allows creating events, listening to an event, and triggering events

## Design

#### Event<L>
- `id: number`
- `name: string`
- `new( id: number, name: string )`

#### EventListener<L>
- `priority: number`
- `once: boolean`
- `listener: L`
- `next: EventListener<L>`
- `muted: boolean`
- `mute( muted: boolean ): boolean`
- `remove(): boolean`

#### EventListenerList<L>
- `event: Event<L>`
- `head: EventListener<L>`
- `add( listener: L, priority: number = 5 ): EventListener<L>`
- `clear(): void`

#### EventSystem
- `lists: EventListenerList[]`
- `on<L>( event: Event<L>, listener: L, priority: number = 5 ): EventListener<L>`
- `once<L>( event: Event<L>, listener: L, priority: number = 5 ): EventListener<L>`
- `off<L>( event: Event<L> ): void`
- `trigger<L>( event: Event<L> ): L`
- `create<L>( name: string ): Event<L>`

### Example

```javascript
events = new EventSystem();

// ExplosionEvent( x, y )
// DeathEvent( player )

Event<ExplosionEvent> EXPLOSION = events.create( "explosion" );
Event<DeathEvent> DEATH = events.create( "death" );

listener = events.on( EXPLOSION, function(x, y) {
  // listen for explosions
});

// trigger explosion event
events.trigger( EXPLOSION )( 10, 4 );

// ignore event for now
listener.mute( true );

// ignore event forever
listener.remove();
```
