# GraphQL
The following repo will contain the notes of Graph ql, a query language.

## **1. Overview**

### **REST (Representational State Transfer)**

REST is an **architectural style** for designing APIs. It uses **HTTP methods** (GET, POST, PUT, DELETE) and **resources** (identified by URLs) to manage data.

Example:

```
GET    /users           → Get all users
GET    /users/1         → Get user with ID 1
POST   /users           → Create a new user
PUT    /users/1         → Update user with ID 1
DELETE /users/1         → Delete user with ID 1
```

### **GraphQL**

GraphQL is a **query language for APIs** and a runtime for executing those queries. Instead of multiple endpoints, it exposes a **single endpoint** where you can query exactly the data you need.

Example:

```graphql
query {
  user(id: 1) {
    id
    name
    email
  }
}
```

---

## **2. Key Differences**

| Feature                     | REST API                                  | GraphQL                                           |
| --------------------------- | ----------------------------------------- | ------------------------------------------------- |
| **Data Fetching**           | Over-fetching or under-fetching is common | Fetch exactly what you need                       |
| **Endpoints**               | Multiple endpoints for resources          | Single endpoint                                   |
| **Versioning**              | Uses versions like `/v1/users`            | No versioning needed – queries evolve over time   |
| **Response Structure**      | Fixed structure                           | Custom structure as per query                     |
| **Batch Requests**          | Needs multiple HTTP requests              | Can combine multiple queries in a single request  |
| **Error Handling**          | HTTP status codes                         | Returns 200 with errors in the `errors` array     |
| **Tooling (Introspection)** | Not native, needs tools like Swagger      | Built-in introspection – self-documenting         |
| **Learning Curve**          | Easier to learn, widely adopted           | Requires understanding of schema, resolvers, etc. |

---

## **3. Example Use Case Comparison**

### Scenario: Get a user and their posts.

#### **REST**

You may have to make **two requests**:

```http
GET /users/1
GET /users/1/posts
```

You also might get more data than you need.

#### **GraphQL**

One request with **precisely defined data**:

```graphql
query {
  user(id: 1) {
    id
    name
    posts {
      id
      title
    }
  }
}
```

Result:

```json
{
  "data": {
    "user": {
      "id": "1",
      "name": "Harsh",
      "posts": [
        { "id": "101", "title": "Intro to GraphQL" },
        { "id": "102", "title": "REST vs GraphQL" }
      ]
    }
  }
}
```

---

## **4. Pros and Cons**

### REST

**Pros:**

* Simpler for basic CRUD operations
* Caching is easy using HTTP
* Familiar and widely supported

**Cons:**

* Over-fetching/under-fetching issues
* Versioning can become complex
* Multiple round-trips for nested data

### GraphQL

**Pros:**

* Fetch exactly what you need
* Single request for complex/nested data
* Strongly typed schema
* Self-documenting

**Cons:**

* Complex queries can be hard to cache
* More complex to implement initially
* Risk of performance issues with poorly optimized resolvers

---

## **5. When to Use What**

### Use **REST** when:

* You need simple APIs with standard CRUD
* You want to leverage HTTP caching
* Your team already has experience with REST

### Use **GraphQL** when:

* Clients need **flexible and efficient** data fetching
* You have **nested or related** data
* You want **schema-based validation and documentation**

---

## **Conclusion**

| Choose GraphQL if you need:                      |
| ------------------------------------------------ |
| - Precise data fetching                          |
| - Single endpoint structure                      |
| - Better handling of relationships (nested data) |

| Stick with REST if:                                |
| -------------------------------------------------- |
| - You want simplicity                              |
| - Your app is CRUD-based                           |
| - You rely heavily on HTTP caching or status codes |

---

## **What is a GraphQL Query?**

A **query** in GraphQL is used to **fetch data** from the server. It’s similar to an HTTP `GET` request in REST. However, unlike REST, in GraphQL, you specify **exactly what fields** you want returned.

---

## **Basic Syntax of a GraphQL Query**

```graphql
query {
  fieldName {
    subField1
    subField2
  }
}
```

* `query` → optional keyword (can be omitted for anonymous queries)
* `fieldName` → the name of the root-level field (defined in your schema's `Query` type)
* `{ subField1, subField2 }` → specifies what data you want returned from that field

---

## **Example Schema**

Imagine a GraphQL schema like:

```graphql
type User {
  id: ID!
  name: String!
  email: String!
}

type Query {
  user(id: ID!): User
  users: [User!]!
}
```

---

## **Example 1: Query for a single user**

```graphql
query {
  user(id: "1") {
    id
    name
    email
  }
}
```

### Server Response:

```json
{
  "data": {
    "user": {
      "id": "1",
      "name": "Harsh",
      "email": "harsh@example.com"
    }
  }
}
```

---

## **Example 2: Using Named Query**

```graphql
query GetUserData {
  user(id: "1") {
    id
    name
  }
}
```

> Named queries are useful for logging, caching, and debugging.

---

## **Example 3: Using Variables**

Instead of hardcoding `"1"`:

### Query:

```graphql
query GetUser($userId: ID!) {
  user(id: $userId) {
    id
    name
  }
}
```

### Variables (sent in request body):

```json
{
  "userId": "1"
}
```

This makes your query **reusable and dynamic**.

---

## **Example 4: Nested Queries**

If the schema allows nested types (e.g., user has posts):

```graphql
query {
  user(id: "1") {
    name
    posts {
      title
      content
    }
  }
}
```

---

## **Optional: Aliases and Fragments**

### Aliases

To query the same field multiple times with different arguments:

```graphql
query {
  user1: user(id: "1") {
    name
  }
  user2: user(id: "2") {
    name
  }
}
```

### Fragments

To reuse parts of a query:

```graphql
fragment userFields on User {
  id
  name
}

query {
  user(id: "1") {
    ...userFields
  }
}
```

---

## **Important Notes**

* **Queries must match the schema**. If you ask for a field not in the schema, you'll get an error.
* **GraphQL is strongly typed**. It will validate your query before running it.
* You can only request **fields that the schema exposes** on the type you're querying.

---

## **Recap**

| Concept     | Description                                    |
| ----------- | ---------------------------------------------- |
| `query`     | Used to fetch data                             |
| Named query | Helpful for debugging and tracing              |
| Variables   | Allow dynamic input                            |
| Aliases     | Fetch the same field with different parameters |
| Fragments   | Reuse field selections across queries          |
| Nesting     | Fetch related/nested data in one request       |

---

# **Main Parts of GraphQL**

GraphQL has the following core components:

1. **Schema**
2. **Type Definitions (typeDefs)**
3. **Queries, Mutations, and Subscriptions**
4. **Resolvers**
5. **Scalars and Custom Types**
6. **Context**
7. **Directives**
8. **Fragments**
9. **Variables**
10. **Introspection (optional but useful)**

---

## 1. **Schema**

The schema is the **blueprint** of your GraphQL API. It defines:

* What data is available
* What operations can be performed
* The relationships between data types

You define the schema using **GraphQL SDL (Schema Definition Language)**.

### Example:

```graphql
type User {
  id: ID!
  name: String!
  email: String!
}

type Query {
  user(id: ID!): User
}
```

> This tells the server: “A `User` has 3 fields, and you can fetch a user by ID.”

---

## 2. **Type Definitions (typeDefs)**

These are the **GraphQL schema strings** that you define in your code (often passed into Apollo Server).

### Example in JavaScript:

```js
const typeDefs = `#graphql
  type User {
    id: ID!
    name: String!
  }

  type Query {
    user(id: ID!): User
  }
`;
```

---

## 3. **Operations: Query, Mutation, Subscription**

### A. **Query** – Read operations (like GET)

```graphql
query {
  user(id: "1") {
    name
  }
}
```

### B. **Mutation** – Write operations (create, update, delete)

```graphql
mutation {
  createUser(name: "Harsh") {
    id
    name
  }
}
```

### C. **Subscription** – Real-time operations (with WebSockets)

```graphql
subscription {
  userAdded {
    id
    name
  }
}
```

> In Apollo Server, **query**, **mutation**, and **subscription** are all optional root types in your schema.

---

## 4. **Resolvers**

Resolvers are **functions** that tell the GraphQL server **how to fetch the data** for a given type or field.

### Example:

For this schema:

```graphql
type Query {
  hello: String
}
```

You write a resolver:

```js
const resolvers = {
  Query: {
    hello: () => 'Hello, Harsh!'
  }
}
```

### Resolver Function Signature:

```js
(parent, args, context, info)
```

| Parameter | Description                                         |
| --------- | --------------------------------------------------- |
| `parent`  | Result from the parent resolver (for nested fields) |
| `args`    | Arguments passed in the query                       |
| `context` | Shared object across all resolvers (e.g. auth, DB)  |
| `info`    | Info about execution state (rarely used)            |

---

## 5. **Scalars and Custom Types**

### Built-in Scalars:

* `Int`
* `Float`
* `String`
* `Boolean`
* `ID`

### Custom Types:

```graphql
type User {
  id: ID!
  profile: Profile
}

type Profile {
  age: Int
  bio: String
}
```

### Custom Scalars:

You can define scalars like `Date`, `Email`, etc., using packages like `graphql-scalars`.

---

## 6. **Context**

The `context` object is available to all resolvers and is used to:

* Authenticate users
* Pass global data (e.g., DB connection)
* Share user session info

### Example:

```js
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: ({ req }) => {
    const token = req.headers.authorization;
    const user = verifyToken(token);
    return { user };
  }
});
```

---

## 7. **Directives**

GraphQL directives let you add **extra behavior** to your queries or schema.

### Common example: `@include` and `@skip`

```graphql
query getUser($withEmail: Boolean!) {
  user(id: "1") {
    name
    email @include(if: $withEmail)
  }
}
```

You can also define custom schema directives like `@auth`, but those require extra setup.

---

## 8. **Fragments**

Fragments allow you to **reuse parts of a query**, especially useful in large apps or when querying the same fields from different places.

```graphql
fragment userFields on User {
  id
  name
  email
}

query {
  user(id: "1") {
    ...userFields
  }
}
```

---

## 9. **Variables**

GraphQL supports **variables** to keep queries dynamic and clean.

```graphql
query GetUser($userId: ID!) {
  user(id: $userId) {
    id
    name
  }
}
```

Variables:

```json
{
  "userId": "1"
}
```

---

## 10. **Introspection**

GraphQL supports introspection, which lets clients query the schema itself. This enables features like:

* Auto-generated docs
* Schema explorers
* Autocomplete in GraphQL tools

```graphql
query {
  __schema {
    types {
      name
    }
  }
}
```

---

## Recap: Key GraphQL Components

| Part           | Purpose                                    |
| -------------- | ------------------------------------------ |
| `Schema`       | The full structure of types and operations |
| `typeDefs`     | SDL definition of the schema               |
| `Query`        | Read operations                            |
| `Mutation`     | Write operations                           |
| `Subscription` | Real-time operations via WebSockets        |
| `Resolvers`    | Functions that return data                 |
| `Scalars`      | Basic types (String, Int, etc.)            |
| `Context`      | Shared object for auth, DB, etc.           |
| `Fragments`    | Reusable field sets                        |
| `Variables`    | Dynamic input to queries/mutations         |
| `Directives`   | Control query behavior (e.g., @include)    |

---




