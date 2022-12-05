
<!-- TOC -->

- [Intro](#intro)
- [References](#references)
  - [specification for GraphQL](#specification-for-graphql)
  - [A dedicated book recommended on GraphQL website](#a-dedicated-book-recommended-on-graphql-website)
  - [Article](#article)
  - [YouTube](#youtube)
  - [Oreilly books](#oreilly-books)
  - [Helion course](#helion-course)
- [Fields](#fields)
  - [Example 1](#example-1)
  - [Example 2](#example-2)
- [Arguments](#arguments)
  - [Example 1](#example-1-1)
  - [Example 2](#example-2-1)
- [Aliases](#aliases)
  - [Example 1](#example-1-2)
  - [Example 2](#example-2-2)
- [Fragments](#fragments)
  - [Example 2](#example-2-3)
  - [Using variables inside fragments](#using-variables-inside-fragments)
- [Operation name](#operation-name)
  - [Ezample 1](#ezample-1)
  - [Ezample 2](#ezample-2)
- [Variables](#variables)
  - [Variable definitions](#variable-definitions)
  - [Default variables](#default-variables)
- [Directives](#directives)
  - [Example 2](#example-2-4)
- [Inline Fragments](#inline-fragments)
  - [Meta fields / Introspection](#meta-fields--introspection)

<!-- /TOC -->

# Intro

GraphQL is often touted for its efficiency when consuming data from APIs. There are two key aspects to its efficiency:

- You get only the data you need
- You get all the data you need in a single request

GraphQL and SQL also have entirely different syntax. Instead of SELECT, GraphQL uses `Query` to request data.

Queries are simply strings that are sent in the body of POST requests to a GraphQL endpoint. 

In the GraphQL query language, fields can be either scalar types or object types. Scalar types are similar to primitives in other languages. They are the leaves of our selection sets. Out of the box, GraphQL comes with five built-in scalar types: `integers (Int)`, `floats (Float)`, `strings (String)`, `Booleans (Boolean)`, and unique `identifiers (ID)`. Both integers and floats return JSON numbers, and String and ID return JSON strings. Booleans just return Booleans. Even though ID and String will return the same type of JSON data, GraphQL still makes sure that IDs return unique strings.

**ctrl + space = list of avaliable scope**


# References

## specification for GraphQL

- http://spec.graphql.org/draft/#

## A dedicated book recommended on GraphQL website

- https://graphql.guide/introduction

## Article

- https://www.digitalocean.com/community/tutorials/understanding-queries-in-graphql

- https://graphql.org/learn/queries/

## YouTube

- https://www.youtube.com/watch?v=omSpI1Nu_pg&ab_channel=TheDumbfounds


## Oreilly books

- Learning GraphQL
  - https://learning.oreilly.com/library/view/learning-graphql/9781492030706/

- GraphQL in Action
  - https://learning.oreilly.com/library/view/graphql-in-action/9781617295683/

## Helion course

- https://helion.pl/users/konto/show/vgraph_w



# Fields

At its simplest, GraphQL is about asking for specific fields on objects.

Queries are the construct used by the client to request specific fields from the server.

## Example 1

```
{
  hero {
    name
    # Queries can have comments!
    friends {
      name
    }
  }
}
```

## Example 2

Given that GraphQL is structured to optimally expose one endpoint for all requests, queries are structured to request specific fields, and the server is equally structured to respond with the exact fields being requested.

Consider a situation where a client wants to request soccer players from an API endpoint. The query will be structured like this:

```
{
    players {
        name
    }
}
```

This is a typical GraphQL query. Queries are made up of two distinct parts:

- The `root field` (players): The object containing the payload.
- The `payload` (name): The field(s) requested by the client.

This is an essential part of GraphQL because the server knows what fields the client is asking for and always responds with that exact data


# Arguments

GraphQL queries allow us to pass in arguments into query fields and nested query objects. You can pass arguments to every field and every nested object in your query to further deepen your request and make multiple fetches. Arguments serve the same purpose as your traditional `query parameters` or `URL segments` in a REST API. 

We can pass them into our query fields to further specify how the server should respond to our request.

In a system like REST, you can only pass a single set of arguments - the query parameters and URL segments in your request. But in GraphQL, every field and nested object can get its own set of arguments, making GraphQL a complete replacement for making multiple API fetches. 

## Example 1

```
{
  human(id: "1000") {
    name
    height
  }
}
```

## Example 2

Back to our earlier situation of fetching a specific player’s kit details like `shirt size` or `shoe size`. First, we’ll have to specify that player by passing in an argument `id` to identify the player from the list of players, and then define the fields we want in the query payload:


```
{
    player(id : "Pogba") {
        name,
        kit {
            shirtSize,
            bootSize
        }
    }
}
```
Here, we are requesting the desired fields on the player `Pogba` because of the `id` argument we passed into the query. Just like fields, there are no type restrictions. Arguments can be of different types, too.


# Aliases

Aliases - they let you rename the result of a field to anything you want.

## Example 1

```
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}
```

## Example 2

```
{
    player1: player(id: "Pogba") {
        name,
        kit {
            shirtSize,
            shoeSize
        }
    }
    player2: player(id: "Lukaku") {
        name,
        kit {
            shirtSize,
            shoeSize
        }
    }
}
```

In the above example, the two `hero` fields would have conflicted, but since we can alias them to different names, we can get both results in one request.

# Fragments

That's why GraphQL includes reusable units called fragments. Fragments let you construct sets of fields, and then include them in queries where you need to.

```
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  appearsIn
  friends {
    name
  }
}
```

Above query would be pretty repetitive if the fields were repeated. The concept of fragments is frequently used to split complicated application data requirements into smaller chunks, especially when you need to combine lots of UI components with different fragments into one initial data fetc

## Example 2

Looking at our query, you’ll notice that the `player` field is practically the same for both players:

```
name,
kit {
    shirtSize,
    bootSize
}
```

To be more efficient with our query, we can extract this piece of shared logic into a reusable fragment on the `player` field, like so:

```
{
    player1: player(id: "Pogba") {
        ...playerKit
    }
    player2: player(id: "Lukaku") {
        ...playerKit
    }
}

fragment playerKit on player {
    name,
    kit {
        shirtSize,
        shoeSize
    }
}
```



## Using variables inside fragments

It is possible for fragments to access variables declared in the query or mutation.

```
query HeroComparison($first: Int = 3) {
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  friendsConnection(first: $first) {
    totalCount
    edges {
      node {
        name
      }
    }
  }
}
```

# Operation name

Here’s an example that includes the keyword `query` as operation type and `HeroNameAndFriends` as operation name:

The operation type is either query, mutation, or subscription and describes what type of operation you're intending to do. 

The operation name is a meaningful and explicit name for your operation. It is only required in multi-operation documents, but its use is encouraged because it is very helpful for debugging and server-side logging.

## Ezample 1

```
query HeroNameAndFriends {
  hero {
    name
    friends {
      name
    }
  }
}
```

## Ezample 2

Until now, we have been using the shorthand operation syntax, where we are not explicitly required to define either the operation name or type. 

In production, it is a best practice to use operation names and types to help make your codebase less ambiguous. It also helps with debugging your query in the case of an error.


The operation syntax comprises of two central things:

- The `operation type` which could be either query, mutation, or subscription. It is used to describe the type of operation you intend to carry out.

- The `operation name` which could be anything that will help you relate with the operation you’re trying to perform.

Now, we can rewrite our earlier example and add an operation type and name like this:

```
query PlayerDetails{
    player(id : "Pogba") {
        name,
        kit {
            shirtSize,
            bootSize
        }
    }
}
```

In this example, `query` is the `operation type` and `PlayerDetails` is the `operation name`.


# Variables

It wouldn't be a good idea to pass these dynamic arguments directly in the query string, because then our client-side code would need to dynamically manipulate the query string at runtime, and serialize it into a GraphQL-specific format. 

Instead, GraphQL has a first-class way to factor dynamic values out of the query, and pass them as a separate dictionary. These values are called variables.


When we start working with variables, we need to do three things:

1. Replace the static value in the query with `$variableName`
2. Declare `$variableName` as one of the variables accepted by the query
3. Pass `variableName: value` in the separate, transport-specific (usually JSON) variables dictionary


```
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

Variable:

```
{
  "episode": "JEDI"
}
```

Now, in our client code, we can simply pass a different variable rather than needing to construct an entirely new query.


## Variable definitions

The variable definitions are the part that looks like `($episode: Episode)` in the query above. It works just like the argument definitions for a function in a typed language. It lists all of the variables, prefixed by `$`, followed by their type, in this case `Episode`.

All declared variables must be either scalars, enums, or input object types. So if you want to pass a complex object into a field, you need to know what input type that matches on the server. Learn more about input object types on the Schema page.

Variable definitions can be optional or required. In the case above, since there isn't an `!` next to the `Episode` type, it's optional. But if the field you are passing the variable into requires a non-null argument, then the variable has to be required as well.

To learn more about the syntax for these variable definitions, it's useful to learn the GraphQL schema language. The schema language is explained in detail on the Schema page.


## Default variables

Default values can also be assigned to the variables in the query by adding the default value after the type declaration.

```
query HeroNameAndFriends($episode: Episode = JEDI) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```


# Directives

We discussed above how variables enable us to avoid doing manual string interpolation to construct dynamic queries. Passing variables in arguments solves a pretty big class of these problems, but we might also need a way to dynamically change the structure and shape of our queries using variables.

GraphQL directives provide us with a way to tell the server whether to `include` or `skip` a particular field when responding to our query. There are two built-in directives in GraphQL that helps us achieve that:

- `@skip`: for skipping a particular field when the value passed into it is true.
- `@include`: to include a particular field when the value passed into it is true.

```
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}
```

Variables

```
{
  "episode": "JEDI",
  "withFriends": true
}
```


We needed to use a new feature in GraphQL called a directive. A directive can be attached to a field or fragment inclusion, and can affect execution of the query in any way the server desires. The core GraphQL specification includes exactly two directives, which must be supported by any spec-compliant GraphQL server implementation:

- `@include(if: Boolean)` Only include this field in the result if the argument is `true`.
- `@skip(if: Boolean)` Skip this field if the argument is `true`.


## Example 2

Let’s add a `Boolean` directive and skip it on the server with the `@skip` directive:

```
query PlayerDetails ($playerShirtDirective: Boolean!){
    player(id: "Pogba") {
        name,
        kit {
            shirtSize @skip(if: $playerShirtDirective)
            bootSize
        }
    }
}
```

The next thing we’ll do is create the `playerShirtDirective` directive in our query variables and set it to true:

```
// Query Variables
{
  "itemIdDirective": true
}
```


# Inline Fragments

Like many other type systems, GraphQL schemas include the ability to define interfaces and union types. Learn about them in the schema guide.

If you are querying a field that returns an interface or a union type, you will need to use inline fragments to access data on the underlying concrete type. It's easiest to see with an example:

```
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    ... on Droid {
      primaryFunction
    }
    ... on Human {
      height
    }
  }
}
```

Variables

```
{
  "ep": "JEDI"
}
```

In this query, the `hero` field returns the type `Character`, which might be either a `Human` or a `Droid` depending on the episode argument. In the direct selection, you can only ask for fields that exist on the `Character` interface, such as `name`.

To ask for a field on the concrete type, you need to use an inline fragment with a type condition. Because the first fragment is labeled as ... `on Droid`, the `primaryFunction` field will only be executed if the `Character` returned from `hero` is of the `Droid` type.

## Meta fields / Introspection

Given that there are some situations where you don't know what type you'll get back from the GraphQL service, you need some way to determine how to handle that data on the client. GraphQL allows you to request `__typename`, a meta field, at any point in a query to get the name of the object type at that point.

One of the most powerful features of GraphQL is introspection. Introspection is the ability to query details about the current API’s schema. Introspection is how those nifty GraphQL documents are added to the GraphiQL Playground interface.

```
{
  search(text: "an") {
    __typename
    ... on Human {
      name
    }
    ... on Droid {
      name
    }
    ... on Starship {
      name
    }
  }
}
```

In the above query, search returns a union type that can be one of three options. It would be impossible to tell apart the different types from the client without the `__typename` field.