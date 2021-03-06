# Overview

<a href="https://github.com/graphcool/graphql-yoga"><img src="https://imgur.com/fTa1vMv.png" alt="Prisma" height="36px"></a>

## Description

GraphQL Middleware is a schema wrapper which allows you to manage additional functionality across multiple resolvers efficiently.

## Features

- **Easiest way to handle GraphQL middleware:** Intuitive, yet familiar API will get under your skin in a second.
- **Powerful:** Allows complete control over your resolvers (Before, After).
- **Compatible:** Works with any GraphQL Schema.

## Install

```sh
yarn add graphql-middleware
```

## Usage

```ts
import { applyMiddleware } from 'graphql-middleware'
import { makeExecutableSchema } from 'graphql-tools'
import { authMiddleware, metricsMiddleware } from './middleware'

// Minimal example middleware (before & after)
const beepMiddleware = {
  Query: {
    hello: async (resolve, parent, args, context, info) => {
      // You can you middleware to override arguments
      const argsWithDefault = { name: 'Bob', ...args }
      const result = await resolve(parent, argsWithDefault, context, info)
      // Or change the returned values of resolvers
      return result.replace(/Trump/g, 'beep')
    },
  },
}

const typeDefs = `
  type Query {
    hello(name: String): String
  }
`
const resolvers = {
  Query: {
    hello: (parent, { name }, context) => `Hello ${name ? name : 'world'}!`,
  },
}

const schema = makeExecutableSchema({ typeDefs, resolvers })

const schemaWithMiddleware = applyMiddleware(
  schema,
  metricsMiddleware,
  authMiddleware,
  beepMiddleware,
)
```

### Usage with `graphql-yoga`

> `graphql-yoga` has built-in support for `graphql-middleware`!

```ts
import { GraphQLServer } from 'graphql-yoga'
import { authMiddleware, metricsMiddleware } from './middleware'

const typeDefs = `
  type Query {
    hello(name: String): String
  }
`
const resolvers = {
  Query: {
    hello: (parent, { name }, context) => `Hello ${name ? name : 'world'}!`,
  },
}

const server = new GraphQLServer({
  typeDefs,
  resolvers,
  fieldMiddleware: [authMiddleware, metricsMiddleware],
  documentMiddleware: [],
})
server.start(() => console.log('Server is running on localhost:4000'))
```

### Examples

## API

A middleware is a resolver function that wraps another resolver function.

```ts
type IMiddlewareFunction = (
  resolve: Function,
  parent: any,
  args: any,
  context: any,
  info: GraphQLResolveInfo,
) => Promise<any>

interface IMiddlewareTypeMap {
  [key: string]: IMiddlewareFunction | IMiddlewareFieldMap
}

interface IMiddlewareFieldMap {
  [key: string]: IMiddlewareFunction
}

type IMiddleware = IMiddlewareFunction | IMiddlewareTypeMap

function applyMiddleware(
  schema: GraphQLSchema,
  ...middleware: IMiddleware[]
): GraphQLSchema
```

## GraphQL Middleware Use Cases

* Logging
* Metrics
* Input sanitisation
* Performance measurement
* Authorization
* Caching
* Tracing

## FAQ

### Can I use GraphQL Middleware without GraphQL Yoga?

Yes. Nevertheless, we encourage you to use it in combination with Yoga. Combining the power of `fieldMiddleware` that GraphQL Middleware offers, with `documentMiddleware` which Yoga exposes, gives you unparalleled control over the execution of your queries.

### How does GraphQL Middleware compare to `directives`?

GraphQL Middleware and `directives` tackle the same problem in a completely different way. GraphQL Middleware allows you to implement all your middleware logic in your code, whereas directives encourage you to mix schema with your functionality.

### Should I modify the context using GraphQL Middleware?

GraphQL Middleware allows you to modify the context of your resolvers, but we encourage you to use GraphQL Yoga's `documentMiddleware` for this functionality instead.

## Help & Community [![Slack Status](https://slack.graph.cool/badge.svg)](https://slack.graph.cool)

Join our [Slack community](http://slack.graph.cool/) if you run into issues or have questions. We love talking to you!

[![GraphCool](http://i.imgur.com/5RHR6Ku.png)](https://www.graph.cool/)