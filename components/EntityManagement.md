# Entity Management

## Description

## Dependencies

- [Core](Core.md)
- [Entity](Entity.md)

## Sub-Components

- Entity Component System
- Scene Graph
- Collections

## Terms

## Design


Entity
- getId ()
- getComponent <T>(id: int): T
- isExpired (): boolean
- expire (): void
- getFlags (): number
- getEntityType (): EntityType

EntityHandle : Entity
- getEntity (): Entity
- isValid (): boolean

EntityType
- getId (): int
- getComponents (): int []
- forEach (callback: (Entity))

EntitySystem
- getIndex (): int
- isActive (): boolean
- setActive (active: boolean)
- getComponents (): int []
- getProcessor (): EntitySystemProcessor
- getRequiredFlags (): number

EntitySystemProcessor
- process (entity: Entity, state: GameState): void

EntityManager
- getEntity (id): Entity 
- hasEntity (id): boolean
- addComponent<T> (memory: Memory<T>): int
- setComponent<T> (memory: Memory<T>, id: int): boolean
- getHandle (id: int): Handle
- addSystem (components: int [], processor: EntitySystemProcessor, requiredFlags: number): EntitySystem
- getSystem (index: int): EntitySystem
- process (state: GameState)



#### Implementation
Goals: you can iterate all components of a given type, there is no wasted space, you can see what other components an entity has, you can iterate over all entities
Components[ cid ] = { data[ k ], entity[ k ] = entityId, free[ j ], update() }
Entities[ entityId ] = { type, typeIndex }
Free[ j ] = entityId
EntityType[ type ] = { components[ i ] = cid, mappings[ typeIndex ][ i ] = k, free }
new Component( initialEntityCapacity, valuesPerEntity, factory, currentValue )
new EntityType( componentIds … )
Tiered updates is achieved by having duplicate of the same component type
Systems iterate over all entities with components X, Y, Z
Entity type map id=>bool given set of components
