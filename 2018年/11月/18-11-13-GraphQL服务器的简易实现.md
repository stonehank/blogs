* 为什么要实现一个`GraphQL`服务器

    通过自己实现，更深入了解`GraphQL`怎么构造，怎么获取，一些术语

* 直接入正题，如何实现一个`GraphQL`服务器

    了解4种类型
    
    * Query
    
        执行内容查询
        
    * Mutation
        
        执行内容变更
        
    * Schema
    
        模式，定义内容结构及类型
        
    * Resolvers
    
        连接`Schema`和`Query`，也就是具体内容。
        

1. 定义Schema

    ```js
    import { gql } from 'apollo-server-express';
    
    const typeDefs = gql`
        type Person {
            id: Int
            name: String
            gender: String
            homeworld: String
        }
        input DataInput {		
            id: Int!
            name: String
            gender: String
            homeworld: String
        }
        type Mutation {
            setPerson(input:DataInput!):Person
        }
        type Query {
            allPeople: [Person]
            person(id: Int!): Person
        }
    `;
    
    export default typeDefs;
    ```
    
    这里定义了1个自定义查询类`Person`，1个自定义修改类`DataInput`，1个`Mutation`方法，2个`Query`方法，其中加`!`表示这个字段不可缺少。

2. 定义`Resolvers`

    ```js
    const defaultData = [
      {
        id: 1,
        name: 'Luke SkyWaler',
        gender: 'male',
        homeworld: 'Tattoine'
      },
      {
        id: 2,
        name: 'C-3PO',
        gender: 'bot',
        homeworld: 'Tattoine'
      }
    ];
    
    const resolvers = {
      Mutation: {
        setPerson: (firstParam, {input}) => {
          let matchIdx = defaultData.length
          const {id} = input
          let matchObj = defaultData.find((obj, i) => {
            if (obj.id === id) {
              matchIdx = i
              return true
            }
          })
          let defaultObj
          if (matchObj) defaultObj = matchObj
          else defaultObj = {name: '', gender: '', homeworld: ''}
          let newObj = Object.assign(defaultObj, input)
          defaultData[matchIdx] = newObj
          return newObj
        }
      },
      Query: {
        allPeople: () => {
          return defaultData;
        },
        person: (root, {id}) => {
          return defaultData.filter(character => {
            return (character.id === id);
          })[0];
        }
      }
    };
    
    export default resolvers;
    ```
    
    这里定义了对`Query`和`Mutation`的具体执行方法，注意，它们的值必须是`Object`或者`Function`。
    
    > PS: 这里我没搞清楚 `Mutation` 第一个参数`firstParam`的作用，一直都是`undefined`

3. 创建服务器

    ```js
    import express from 'express';
    import {ApolloServer, gql } from 'apollo-server-express';
    import typeDefs from './api/schema';
    import resolvers from './api/resolvers';
    
    const server = new ApolloServer({
      typeDefs,
      resolvers,
    });
    
    const app = express();
    server.applyMiddleware({ app });
    
    app.get('/', (req, res) => res.send('Babel Working!'));
    
    app.listen({ port: 4000 }, () =>
      console.log(`🚀 Server ready at http://localhost:4000${server.graphqlPath}`)
    );
    ```

4. package.json

    ```
    {
      "name": "gh-demo",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
      "dev": "babel-watch index.js"
    },
      "keywords": [],
      "author": "",
      "license": "ISC",
      "dependencies": {
        "apollo-server-express": "^2.2.1",
        "express": "^4.16.4",
        "graphql": "^14.0.2"
    },
      "devDependencies": {
      "babel-cli": "^6.26.0",
        "babel-preset-env": "^1.7.0",
        "babel-watch": "^2.0.7"
    }
    }
    ```

参考：[Creating a GraphQL server with NodeJS](https://medium.com/crowdbotics/creating-a-graphql-server-with-nodejs-ef9814a7e0e6)