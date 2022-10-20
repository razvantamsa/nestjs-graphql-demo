# step 1 -> create new project

`mkdir ... -> cd ...` \
`npm init -y`
<br><br>

# step 2 -> install dependencies

### graphql - also known as graphql-js is the library that implements the core GQL parsing and exec algos
### @apollo/server - main library for apollo server itself
###     apollo server knows how to turn HTTP reqs and resp into GQL ops and run in an extensible cxt
    
`npm install @apollo/server graphql`

## typescript setup
<hr>

`mkdir src` \
`touch src/index.ts` \
`npm install --save-dev typescript @types/node`

### create tsconfig.json

```
{
  "compilerOptions": {
    "rootDirs": ["src"],
    "outDir": "dist",
    "lib": ["es2020"],
    "target": "es2020",
    "module": "esnext",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "types": ["node"]
  }
}
```

### replace default scripts in package.json with
- ### the start script tells typescript to compile the code into js before using node to run that code
- ### setting the project's type to module loads your js files as es modules -> top level await calls
```
{
  // ...etc.
  "type": "module",
  "scripts": {
    "compile": "tsc",
    "postinstall": "npm run compile",
    "start": "npm run compile && node ./dist/index.js"
  }
  // other dependencies
}
```
<br><br>

# step 3 -> define your graphql schema

### every graphql server uses a schema to define the structure of data that clients can query
### example

### index.ts
```
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';

// A schema is a collection of type definitions (hence "typeDefs")
// that together define the "shape" of queries that are executed against
// your data.
const typeDefs = `#graphql
  # Comments in GraphQL strings (such as this one) start with the hash (#) symbol.

  # This "Book" type defines the queryable fields for every book in our data source.
  type Book {
    title: String
    author: String
  }

  # The "Query" type is special: it lists all of the available queries that
  # clients can execute, along with the return type for each. In this
  # case, the "books" query returns an array of zero or more Books (defined above).
  type Query {
    books: [Book]
  }
`;
```

### adding #graphql to the beginning of a template literal provides graphql syntax highlighting in supporting IDEs
<br><br>

# step 4 -> define your data set

### apollo server can fetch data from any source you connect to 
### (including a database, a REST API, a static object storage or even a GQL server)

### index.ts
```
const books = [
  {
    title: 'The Awakening',
    author: 'Kate Chopin',
  },
  {
    title: 'City of Glass',
    author: 'Paul Auster',
  },
];
```
<br><br>

# step 5 -> define a resolver

### we've defined our data set, but apollo server doesn't know that it should use that data set when it's executing queries
### -> solution is to create a resolver (tells apollo server how to fetch the data associated with a particular type)

### index.ts
```
// Resolvers define how to fetch the types defined in your schema.
// This resolver retrieves books from the "books" array above.
const resolvers = {
  Query: {
    books: () => books,
  },
};
```
<br><br>

# step 6 -> create an instance of apolloserver

### with a schema, data set and resolver defined, now we need to provide this information to apollo server when we initialize it

### index.ts
```
// The ApolloServer constructor requires two parameters: your schema
// definition and your set of resolvers.
const server = new ApolloServer({
  typeDefs,
  resolvers,
});

// Passing an ApolloServer instance to the `startStandaloneServer` function:
//  1. creates an Express app
//  2. installs your ApolloServer instance as middleware
//  3. prepares your app to handle incoming requests
const { url } = await startStandaloneServer(server, {
  listen: { port: 4000 },
});

console.log(`🚀  Server ready at: ${url}`);
```
<br><br>

# step 7 -> start the server and execute your first query
`npm start` \
`🚀  Server ready at: http://localhost:4000/`

### to execute a query we use the apollo sandbox
### the sandbox UI includes
- ### an operations panel for writing and executing queries
- ### a response panel for viewing query results
- ### tabs for schema exploraton, search and settings
- ### URL bar for connecting to other gql servers


### query example
```
query GetBooks {
  books {
    title
    author
  }
}
```
