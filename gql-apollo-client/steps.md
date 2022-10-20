# step 1 -> setup

### create a new react project with create-react-app
<br><br>

# step 2 -> install dependencies
### apps that use apollo client require two top-level dependencies
### @apollo/client - everything to set up apollo client (in-mem cache, local state mgmt, err handling etc...)
### graphql - provides logic for parsing GQL queries

`npm install @apollo/client graphql`
<br><br>

# step 3 -> initialize apollo client

### index.js - import the symbols needed from @apollo/client
```
import { ApolloClient, InMemoryCache, ApolloProvider, gql } from '@apollo/client';

```
### index.js - initialize an ApolloClient instance
```
const client = new ApolloClient({
  uri: 'https://flyby-gateway.herokuapp.com/',
  cache: new InMemoryCache(),
});
```

### uri - URL of our GraphQL server
### cache - instance of InMemoryCache which apollo client uses to cache query results after fetching them

### index.js - sending a query with plain javascript by calling client.query() with the query string:
```
// const client = ...

client
  .query({
    query: gql`
      query GetLocations {
        locations {
          id
          name
          description
          photo
        }
      }
    `,
  })
  .then((result) => console.log(result));
```

<br><br>

# step 4 - connect your client to react

### connect apollo client to react with the apolloprovider component
### wraps your react app and places apollo client on the context, enabling you to access it 
### from anywhere in the component tree

### index.js
```
import React from 'react';
import * as ReactDOM from 'react-dom/client';
import { ApolloClient, InMemoryCache, ApolloProvider } from '@apollo/client';
import App from './App';

const client = new ApolloClient({
  uri: 'https://flyby-gateway.herokuapp.com/',
  cache: new InMemoryCache(),
});

// Supported in React 18+
const root = ReactDOM.createRoot(document.getElementById('root'));

root.render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
);
```
<br><br>

# step 5 - fetch data with useQuery

### after ApolloProvider is hooked up, requests can be done with useQuery
### useQuery - react hook that shares GraphQL data with UI

### App.js

```
// Import everything needed to use the `useQuery` hook
import { useQuery, gql } from '@apollo/client';

export default function App() {
  return (
    <div>
      <h2>My first Apollo app 🚀</h2>
    </div>
  );
}
```

### App.js - define the query we want to execute by wrapping in the gql template literal
```
const GET_LOCATIONS = gql`
  query GetLocations {
    locations {
      id
      name
      description
      photo
    }
  }
`;
```

### App.js - define the query we want to execute by wrapping in the gql template literal
```
function DisplayLocations() {
  const { loading, error, data } = useQuery(GET_LOCATIONS);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error :(</p>;

  return data.locations.map(({ id, name, description, photo }) => (
    <div key={id}>
      <h3>{name}</h3>
      <img width="400" height="250" alt="location-reference" src={`${photo}`} />
      <br />
      <b>About this location:</b>
      <p>{description}</p>
      <br />
    </div>
  ));
}
```

<br><br>