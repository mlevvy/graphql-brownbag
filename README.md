# Introduction to GraphQL

## Theory
### REST disadvantages
* Can't (without custom crafting), select which part of response you want to receive. You always get everything.
* Resource based architecture may cause multiple request in order to display one page.
* It's not fast & rapid. Frontend people always have to wait for backend people.

### What is different
* GraphQL has proper [specification](https://graphql.github.io/graphql-spec).
* Each service has it own schema (something like SOAP).
* GraphQL uses HTTP POST method as an envelope (something like SOAP).
* Furthermore it is declarative.

### The tools
* Explore GitHub's GraphQL [schema](https://developer.github.com/v4/public_schema/). Easy to read?
* Open GraphQL [GraphQL Voyager](https://apis.guru/graphql-voyager/) and browse GitHub schema. What can you see?
* Open GitHub [GraphiQL](https://developer.github.com/v4/explorer/) and run very first request using GraphQL. 

Be careful! You are working on your real, live, production data.

```
query { 
  viewer { 
    login
  }
}
```

# Using GraphQL
## GitHub
### Query and mutation
* You can do `query` or `mutation`. If you do not specify anything, it will be assumed that this is a query. Check it and type:
```
{ 
  viewer { 
    login
  }
}
```

### Query params and declare response
* You can query for example repository by its `name` and `owner`. 
```
query { 
  repository(owner: "git", name:"git") {
    id
    forkCount
  }
}
```
### Read the docs
**TASK** 

What else can you query? Can you lookup repository by the number of stars it has?
<details><summary>Answer</summary>
<p>See the documentation about queryable documents. You can not lookup by the number stars - at least now.</p>
</details>

### Find latest forks
**TASK** 

* A) Find and select 10 people who made fork of `git` repository.
* B) Find and select 10 latest people who made fork of `git` repository.
* C) Find and select 10 latest people who made fork of `git` repository. Also return a number of forks.

<details><summary>Answer</summary>
<p>

```
{
     repository(owner: "git", name: "git") {
       id
       name
       forks(first:10, orderBy:{field: CREATED_AT, direction: DESC}) {
         edges {
           node {
             createdAt
             owner {
               login
             }
           }
         }
       }
       forkCount
     }
   }
```

</p>
</details>

### Using variables
You can use dynamic variables into the query string. Look at this example:
```
query($limit:Int!) {
  repository(owner: "git", name: "git") {
    id
    name
    forks(first:$limit, orderBy:{field: CREATED_AT, direction: DESC}) {
      edges {
        node {
          createdAt
          owner {
            login
          }
        }
      }
    }
    forkCount
  }
}

{
  "limit": 10
}
```

### Find latest stargazers.
**TASK** 
Find and select 10 latest people who gave a star to the `git` repository.

<details><summary>Answer</summary>
<p>

```
{
  repository(owner: "git", name:"git") {
    id
    stargazers(first:10, orderBy:{field:STARRED_AT, direction:DESC}) {
      edges {
        node {
          id
          name
        }
      }
    }
  }
}

```

</p>
</details>

### First modification
In order to change something, use `mutation` keyword. You have different methods, to change different things. 

You have the `addStar` method. The thing you want to send the to server is in its argument. Here we have one argument with the name `input`. 

```
mutation($id:ID!){
  addStar(input:{starrableId: $id}){
    clientMutationId
  }
}

{
  "id":"MDEwOlJlcG9zaXRvcnkzNjUwMg"
}
```

### Modify and return meaningful response
**TASK**

1) Find id of the repository: `mlevvy/graphql-brownbag`
2) Write request to star this repository

Return to the client:
3) Total number of stargazers
4) 5 latest stargazers

<details><summary>Answer</summary>
<p>

```
mutation($id:ID!){
  addStar(input:{starrableId: $id}){
    clientMutationId
    starrable {
          stargazers(first:10, orderBy:{field:STARRED_AT, direction:DESC}) {
            totalCount
            edges {
              node {
                name
              }
            }
          }
        }
  }
}

{
  "id":"MDEwOlJlcG9zaXRvcnkxNzk3NDc3NDI="
}
```
</p>
</details>

### Envelope
Firstly, get your token to use the HTTP [API](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line). 

As you can see, query it is a `string` inside `JSON` object.
```
curl -H "Authorization: bearer TOKEN_GOES_HERE" -X POST -d " \
 { \
   \"query\": \"query { viewer { login }}\" \
 } \
" https://api.github.com/graphql
```

# The Schema
TODO

# The Server
TODO