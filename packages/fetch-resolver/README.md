# fetch-resolver

Generate custom fetch resolver functions for REST and GraphQL using a JSON definition

```js
import createFetchResolver from 'fetch-resolver'

// Example 01
// ----------
// create a resolver for a simple GET to a public REST API
const resolver01 = createFetchResolver({
    type: 'rest',
    url: 'https://jsonplaceholder.typicode.com/users',
})

const res01 = await resolver01()
// -> [ full response from typicode ]


// Example 02
// ----------
// create a resolver that can take in parameters
// re-shape the API data in order to match custom needs
const resolver02 = createFetchResolver({
    type: 'rest',
    url: 'https://jsonplaceholder.typicode.com/users/{{ id }}',
    shape: {
        id: 'id',
        name: 'name',
        address: '{{ address.street }}, {{ address.city }}',
    }
})

const res02 = await resolver02({ id: 1 })
// -> { id: 1, name: 'xxx', address: 'xxx' }


// Example 03
// ----------
// add custom headers to the request (with parameters)
// grab just a piece of the returned data and reshape it
const resolver03 = createFetchResolver({
    type: 'rest',
    url: 'https://jsonplaceholder.typicode.com/users/{{ id }}',
    headers: {
        'X-Origin': 'fetch-resolver',
        'X-RequestID': '{{ id }}',
    },
    grab: 'address',
    shape: {
        value: '{{ street }}, {{ city }}',
        loc: [ 'geo.lat', 'geo.lng' ],
    }
})

const res03 = await resolver03({ id: 1 })
// -> { value: 'xxx', loc: [ lat, lng ]}


// Example 04
// ----------
// compose a GraphQL request
const resolve04 = createFetchResolver({
    type: 'graphql',
    url: 'https://countries.trevorblades.com/',
    query: 'query foo ($code: String!) { country (code: $code) { code name phone currency }}',
    variables: { code: '{{ the.code }}' },
    grab: 'data.country.code',
})

const res04 = await resolve04({ the: { code: 'US' }})
// -> "US"
// note that we build a custom "variable" description that is capable of using the actual set
// of variables that are passed down to the resolver handler.
// this way we can use a nested JSON structure as data origin and re-shape the GraphQL's variables
// in a decent and declarative way.
```

> Take a look at the tests to see other usecases with POST and GraphQL requests.




