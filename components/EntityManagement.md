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
