---
title: Relay
description: A Relay implementation in Graphene
---

# Relay

Graphene have complete support of [Relay](https://facebook.github.io/relay/docs/graphql-relay-specification.html), and it offers some utils for working if from Python easy.

## Nodes

A `Node` is an Interface provided by `graphene.relay` that contains a single field `id` (which is a `ID!`). Any object that inherits from it have to implement a `get_node` method for retrieving a `Node` by an *id*.

Example usage (taken from the [Starwars Relay example](https://github.com/graphql-python/graphene/blob/master/examples/starwars_relay/schema.py)):

```python
class Ship(relay.Node):
    '''A ship in the Star Wars saga'''
    name = graphene.String(description='The name of the ship.')

    @classmethod
    def get_node(cls, id, info):
        return get_ship(id)
```

The `id` returned by the `Ship` type when you query it will be a scalar which contains the enough info for the server for knowing it's type and it's id.

For example, the instance `Ship(id=1)` will return `U2hpcDox` as the id when you query it (which is the base64 encoding of `Ship:1`), and which could be useful later if we want to query a node by its id.


## Connection

A connection is a vitaminized version of a List that provides ways of slicing and paginating through it. The way you create Connection fields in `graphene` is using `relay.ConnectionField`.

You can create connection fields in any ObjectType, but the connection **must** be linked to an object which inherits from `Node` (in this case, a `Ship`).

```python
class Faction(graphene.ObjectType):
    name = graphene.String()
    ships = relay.ConnectionField(Ship)

    def resolve_ships(self, args, info):
        return []
```

## Node Root field

As is required in the [Relay specification](https://facebook.github.io/relay/graphql/objectidentification.htm#sec-Node-root-field), the server must implement a root field called `node` that returns a `Node` Interface.

For this reason, `graphene` provides the field `relay.NodeField`, which links to any type in the Schema which inherits from `Node`. Example usage:

```python
class Query(graphene.ObjectType):
    node = relay.NodeField()
```


## Useful links

* [Getting started with Relay](https://facebook.github.io/relay/docs/graphql-relay-specification.html)
* [Relay Global Identification Specification](https://facebook.github.io/relay/graphql/objectidentification.htm)
* [Relay Cursor Connection Specification](https://facebook.github.io/relay/graphql/connections.htm)
* [Relay input Object Mutation](https://facebook.github.io/relay/graphql/mutations.htm)
