# graphql-go

A learning project exploring GraphQL API development in Go using [gqlgen](https://gqlgen.com/) and SQLite.

## Overview

This project implements a simple **course management API** with categories and courses. It covers the fundamentals of building a GraphQL server in Go: schema definition, code generation, resolver implementation, and database persistence.

**Stack:**
- **Go** — application language
- **gqlgen** — GraphQL server code generator
- **SQLite** — embedded database via `go-sqlite3`
- **GraphQL Playground** — interactive API explorer

## Project Structure

```
.
├── graph/
│   ├── generated.go          # Auto-generated GraphQL execution engine (do not edit)
│   ├── model/
│   │   └── models_gen.go     # Auto-generated Go structs from the schema (do not edit)
│   ├── resolver.go           # Dependency injection for resolvers
│   ├── schema.graphqls       # GraphQL schema definition
│   └── schema.resolvers.go   # Resolver implementations (business logic lives here)
├── internal/
│   └── database/
│       └── category.go       # Category database operations
├── gqlgen.yml                # gqlgen code generation configuration
├── server.go                 # Application entry point
├── go.mod
└── go.sum
```

## Prerequisites

- [Go 1.21+](https://go.dev/dl/)
- GCC (required to compile `go-sqlite3` via CGO)
  - **Linux/macOS:** usually pre-installed
  - **Windows:** install [TDM-GCC](https://jmeubank.github.io/tdm-gcc/) or use WSL

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/joaogbrieldev/graphql.git
cd graphql
```

### 2. Install dependencies

```bash
go mod tidy
```

### 3. Create the database

```bash
sqlite3 data.db "CREATE TABLE IF NOT EXISTS categories (id TEXT PRIMARY KEY, name TEXT NOT NULL, description TEXT);"
```

### 4. Run the server

```bash
go run server.go
```

The server starts on port `8080`. Open `http://localhost:8080/` to access the **GraphQL Playground**.

To use a different port:

```bash
PORT=9090 go run server.go
```

## GraphQL API

### Schema

```graphql
type Category {
  id: ID!
  name: String!
  description: String
  courses: [Course!]!
}

type Course {
  id: ID!
  name: String!
  description: String
  category: Category!
}

type Query {
  categories: [Category!]!
  courses: [Course!]!
}

type Mutation {
  createCategory(input: NewCategory!): Category!
  createCourse(input: NewCourse!): Course!
}
```

### Example Operations

**Create a category:**

```graphql
mutation {
  createCategory(input: { name: "Programming", description: "Software development courses" }) {
    id
    name
    description
  }
}
```

**List all categories:**

```graphql
query {
  categories {
    id
    name
    description
  }
}
```

## Code Generation

This project uses `gqlgen` to auto-generate the GraphQL execution engine and model structs. After modifying [graph/schema.graphqls](graph/schema.graphqls), regenerate with:

```bash
go run github.com/99designs/gqlgen generate
```

> **Note:** Never manually edit `graph/generated.go` or `graph/model/models_gen.go` — they are overwritten on every generation.

## Learning Resources

- [gqlgen Getting Started](https://gqlgen.com/getting-started/)
- [GraphQL Specification](https://spec.graphql.org/)
- [How to GraphQL](https://www.howtographql.com/)
- [go-sqlite3](https://github.com/mattn/go-sqlite3)
