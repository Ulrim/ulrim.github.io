---
title: "Express+MongoDB+GraphQL #2 CRUD"
date: 2019-09-25 20:08:00 -0400
categories: 
---

이어서...
---

### 1. 폴더 생성 및 환경설정
폴더 생성 : `mkdir mongodb_graphql`
폴더 들어가기 : `cd mongodb_graphql`

이후 `yarn init` 또는 `npm init` 으로 `package.json`을 만들어줍니다.

### 1-1. express, graphql, mongoose를 설치.
`npm install --save express express-graphql graphql-tools mongoose`

express: nodejs 프레임워크
Mongoose : MongoDB 접근
graphql-tools: 스키마 생성

### 1-2. 바벨 설치
여기선 ES6을 사용할 것입니다!
바벨을 설치해줍시다.
`npm install --save-dev babel-cli babel-preset-env babel-preset-stage-3`

#### ES6 환경과 Babel를 모르신다면 여기를!!

Poiemaweb : https://poiemaweb.com/es6-babel-webpack-1

### 1-3. nodemon 설치 및 기타 설정
그리고 마지막으로 nodemon 설치와 바벨 설정을 해주겠습니다.
`npm install nodemon` : nodemon은 실시간으로 코드가 변경되면 그에따라 웹페이지에도 적용을 시켜준답니다.

먼저 해당 폴더에 **.babelrc** 파일을 생성해줍니다. 다음과 같이 작성해줍니다.

**.babelrc**
```
{
	"presets": [
    	"env",
        "stage-3"
    ]
}
```
**package.json**
```
{
  "name": "mongodb_graphql",
  "version": "1.0.0",
  "description": "monogodb_graphql",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "express": "^4.17.1",
    "express-graphql": "^0.8.0",
    "graphql-tools": "^4.0.5",
    "mongoose": "^5.6.2",
    "nodemon": "^1.19.1"
  },
  "devDependencies": {
    "babel-cli": "^6.26.0",
    "babel-preset-env": "^1.7.0",
    "babel-preset-stage-3": "^6.24.1"
  },
  "scripts": {<- 이 부분 추가 npm start 사용
    "start": "nodemon --exec babel-node index.js"
  }
}
```
### 2. Express 설정
```
// index.js
import express from 'express';
const app = express();
const port = 3000;

app.get('/', (req, res) => {
    res.json({
        msg: '안녕'
    });
});

app.listen(port, () => {
    console.log(`서버 실행!! 포트는? ${port}`);
});
```

express 서버를 구축하였습니다. 서버를 실행시켜보세요.
```
npm start
```

### 3. MongoDB 설정
이제 현재 내용에 MongoDB를 추가해봅시다!

### 3-1. import Mongoose
```
// index.js
import express from 'express';
import mongoose from 'mongoose'; // new

const app = express();
const port = 3000;
const dbName = "test"; // new
// MongoDB connect 주소 복사 ( 자신의 password 입력 )
const uri = `mongodb+srv://user_01:<password>@ulrim-qjcii.gcp.mongodb.net/${dbName}?retryWrites=true&w=majority`
mongoose.Promise = global.Promise; // new ( 비동기 처리 )
mongoose.connect(uri, { useNewUrlParser: true }); // new

app.get('/', (req, res) => {
    res.json({
        msg: '안녕'
    });
});

app.listen(port, () => {
    console.log(`서버 실행!! 포트는? ${port}`);
});
```
수정하셨다면 오류없이 서버가 실행되어야합니다!

Mongoose를 사용하여 MongoDB에 접근 할 것입니다.
그리고 mongoose는 비동기 처리를 하기위해 Promise를 사용하였습니다.
uri는 MongoDB 클러스터를 불러온 것입니다. (1편 참고)

### 4. GraphQL
### 4-1. GraphQL 설정
```
// index.js
import express from 'express';
import mongoose from 'mongoose';
import graphlHTTP from 'express-graphql'; // new
import schema from './schema'; // new 현재 폴더에 파일 추가 schema.js

const app = express();
const port = 3000;
const dbName = "test";
const uri = `mongodb+srv://user_01:wjdtjr321@ulrim-qjcii.gcp.mongodb.net/${dbName}?retryWrites=true&w=majority`
mongoose.Promise = global.Promise;
mongoose.connect(uri, { useNewUrlParser: true });

app.get('/', (req, res) => {
    res.json({
        msg: '안녕'
    });
});

app.use(`/graphql`, graphlHTTP({
    schema: schema,
    graphiql: true
})); // new

app.listen(port, () => {
    console.log(`서버 실행!! 포트는? ${port}`);
});
```

express-graphql로 express와 graphql을 이어주었고 '**graphiql**'로 graphql 편집기를 호출 할 예정입니다.

수정 후 실행이 되셔야합니다.

### 4-2. GraphQL 스키마
현재 폴더에 `schema.js` 파일 생성
```
// schema.js
import { makeExecutableSchema } from 'graphql-tools';
import { resolvers } from './resolvers' // resolvers.js 파일 생성

const typeDefs = `
    type User {
        _id: ID!
        name: String!
        age: Int!
        gender: String!
    }

    type Query {
        allUser: [User]
    }
`;

const schema = makeExecutableSchema({
    typeDefs,
    resolvers
});

export default schema;
```
GraphQL는 typeDefs, resolvers를 정의해줘야합니다.
typeDefs에서는 어떤 타입으로 데이터를 받을 것인가에 대해 정의를 하고
schema에서는 스키마를 실행가능하게  만들어줍니다.

### 4-3. GraphQL Resolver
현재 폴더에 `resolvers.js` 파일 생성
```
// resolvers.js
import User from './models/user';
export const resolvers = {
    Query: {
        async allUser() {
            return await User.find();
        }
    }
}
```

리졸버는 값을 반환하는 역할을 합니다.

### 4-4. GraphQL-MongoDB 객체 가져오기
`models`폴더를 생성하고 `User.js`파일 생성
```
// ./models/User.js
import mongoose from 'mongoose';

const Schema = mongoose.Schema;

const UserSchema = new Schema({
    name: {
        type: String,
        required: true
    },
    age: {
        type: Number
    },
    gender: {
        type: String
    }
});

export default mongoose.model('user', UserSchema);
```
UserSchema 안에 정의는 MongoDB 정의를 따릅니다. ( graphql 아님 )
숫자 표현을 보시면 아실겁니다.
`graphql`: `Int`, `mongodb`: `Number` 로 정의 되어있습니다.

### 4-5. GraphiQL 확인하기
먼저 GraphiQL을 확인해봅시다.
서버가 실행이 안되어있으신분은 서버를 실행 시켜줍니다.
그 후 `localhost:3000/graphql`로 접속해줍니다.

![14.JPG](https://images.velog.io/post-images/kjs100184/d8ef65a0-9ccc-11e9-884a-afa930f1cd3b/14.JPG)

**GraphiQL에서 쿼리문을 사용하여 데이터베이스 내용을 확인 할 수 있습니다.**
현재는 없으니 나오지않네요..

### 5. CRUD 해보기
### 5-1. Create
먼저 `resolvers.js`에 Mutation을 작성해줍니다. 변화를 시켜주는 작업이라고 생각하시면 됩니다.
**resolvers.js**

```
import User from './models/user';
export const resolvers = {
    Query: {
        async allUser() {
            return await User.find();
        }
    },
    Mutation: {
        async createUser(root, { input }) {
            return await User.create(input);
        }
    } // new
}
```
**Schema.js**
```
import { makeExecutableSchema } from 'graphql-tools';
import { resolvers } from './resolvers'

const typeDefs = `
    type User {
        _id: ID!
        name: String!
        age: Int!
        gender: String!
    }

    type Query {
        allUser: [User]
    }

    input UserInput {
        name: String!
        age: Int!
        gender: String!
    }

    type Mutation {
        createUser(input: UserInput): User
    }
`;

const schema = makeExecutableSchema({
    typeDefs,
    resolvers
});

export default schema;
```
`schema.js`: 정의
`resolvers.js`: 반환
둘은 짝꿍이기때문에 한쪽이 변경되면 다른 한쪽도 같이 맞춰서 가줘야합니다.

### 5-1-1. create 요청 보내기 및 데이터 확인
한번 데이터를 생성해봅시다.
새로고침을 해주시고 다음과 같이 데이터를 생성해봅니다.

![15.JPG](https://images.velog.io/post-images/kjs100184/a37ec120-9cce-11e9-b920-03647a5c6644/15.JPG)

생성이 잘 되었나 확인하시고 `query`로 한번 더 확인 해봅시다.

![16.JPG](https://images.velog.io/post-images/kjs100184/d9ca2350-9cce-11e9-9891-0310c99b6601/16.JPG)

당연히 같은 값이 나오겠죠??
그럼 MongoDB에서 확인 해볼까요?


![17.JPG](https://images.velog.io/post-images/kjs100184/32248bd0-9ccf-11e9-9891-0310c99b6601/17.JPG)

**Collections 클릭**

![18.JPG](https://images.velog.io/post-images/kjs100184/4b466a20-9ccf-11e9-9891-0310c99b6601/18.JPG)

**입력했던 값이 잘 나오고있군요.**
만들기는 성공적이군요!!!

### 5-2. Read
read는 query를 통해서 해보셨죠.. 한번 id값으로 조회를 해보도록 하겠습니다.
**schema.js (getUser 추가)**
```
import { makeExecutableSchema } from 'graphql-tools';
import { resolvers } from './resolvers'

const typeDefs = `
    type User {
        _id: ID!
        name: String!
        age: Int!
        gender: String!
    }

    type Query {
        getUser(_id: ID!): User
        allUser: [User]
    }

    input UserInput {
        name: String!
        age: Int!
        gender: String!
    }

    type Mutation {
        createUser(input: UserInput): User
    }
`;

const schema = makeExecutableSchema({
    typeDefs,
    resolvers
});

export default schema;
```
**resolvers.js (getUser 추가)**
```
import User from './models/user';
export const resolvers = {
    Query: {
        async getUser(root, { _id }){
            return await User.findById(_id);
        }, // new
        async allUser() {
            return await User.find();
        }
    },
    Mutation: {
        async createUser(root, { input }) {
            return await User.create(input);
        }
    }
}
```
다시 새로고침을 하고... id값으로 조회 해봅시다.

![19.JPG](https://images.velog.io/post-images/kjs100184/9e1e5770-9cd0-11e9-b920-03647a5c6644/19.JPG)

다들 데이터가 잘 나오셨나요??
이렇게 id값 하나만 적고 필요한 데이터 종류를 적으면 자동으로 찾아서 출력해준답니다!! **미쳤어...**

### 5-3. Update
여세를 몰아 업데이트도 해볼까요?
**schema.js (updateUser 추가)**
```
import { makeExecutableSchema } from 'graphql-tools';
import { resolvers } from './resolvers'

const typeDefs = `
    type User {
        _id: ID!
        name: String!
        age: Int!
        gender: String!
    }

    type Query {
        getUser(_id: ID!): User
        allUser: [User]
    }

    input UserInput {
        name: String!
        age: Int!
        gender: String!
    }

    type Mutation {
        createUser(input: UserInput): User
        updateUser(_id: ID!, input: UserInput): User
    }
`;

const schema = makeExecutableSchema({
    typeDefs,
    resolvers
});

export default schema;
```
**resolvers.js (updateUser 추가)**
```
import User from './models/user';
export const resolvers = {
    Query: {
        async getUser(root, { _id }){
            return await User.findById(_id);
        }, // new
        async allUser() {
            return await User.find();
        }
    },
    Mutation: {
        async createUser(root, { input }) {
            return await User.create(input);
        },
        async updateUser(root, { _id, input }) {
            return await User.findOneAndUpdate(
                { _id }, 
                input, 
                { new: true }
            );
        }
    }
};
```
새로고침을 하고.. 확인 해볼까요?

![20.JPG](https://images.velog.io/post-images/kjs100184/e84f4d80-9cd1-11e9-884a-afa930f1cd3b/20.JPG)

이름, 나이, 성별 바뀐걸 확인하셨나요?
좋군요.. 완벽해요...
이제 마지막 삭제를 해봅시다!

### 5-4. Delete
드디어 마지막이군요.. 삭제는 input 값이 없이 id값만 입력하면 삭제가 되겠습니다.
**schema.js (deleteUser 추가)**
```
import { makeExecutableSchema } from 'graphql-tools';
import { resolvers } from './resolvers'

const typeDefs = `
    type User {
        _id: ID!
        name: String!
        age: Int!
        gender: String!
    }

    type Query {
        getUser(_id: ID!): User
        allUser: [User]
    }

    input UserInput {
        name: String!
        age: Int!
        gender: String!
    }

    type Mutation {
        createUser(input: UserInput): User
        updateUser(_id: ID!, input: UserInput): User
        deleteUser(_id: ID!): User
    }
`;

const schema = makeExecutableSchema({
    typeDefs,
    resolvers
});

export default schema;
```
**resolvers.js (deleteUser 추가)**
```
import User from './models/user';
export const resolvers = {
    Query: {
        async getUser(root, { _id }){
            return await User.findById(_id);
        }, // new
        async allUser() {
            return await User.find();
        }
    },
    Mutation: {
        async createUser(root, { input }) {
            return await User.create(input);
        },
        async updateUser(root, { _id, input }) {
            return await User.findOneAndUpdate(
                { _id }, 
                input, 
                { new: true }
            );
        },
        async deleteUser(root, { _id }) {
            return await User.findOneAndDelete({ _id });
        }
    }
};
```
다시 실행 시킨뒤 삭제를 시켜봅시다.

![21.JPG](https://images.velog.io/post-images/kjs100184/ece90470-9cd2-11e9-9891-0310c99b6601/21.JPG)

잘 나오는군요! query를 통해서 조회를 해볼까요?

![22.JPG](https://images.velog.io/post-images/kjs100184/03a0c090-9cd3-11e9-b920-03647a5c6644/22.JPG)

음.. 역시 아무것도 나오지않는군요!! mongoDB를 접속해도 데이터는 남아있지 않을꺼에요!!

## 마무리
험난한 여정이 끝났군요.. 
여기서 많은걸 배웠을꺼라고 봅니다. 
**ES6**을 사용했고 **Nodejs(express) 서버**도 구축하였고 **Mongoose(MongoDB), GraphQL** 사용하는 법과 Nodejs에서 **CRUD(Create, Read, Update, Delete)** 작업까지... 
이것만 있으면 이제 **응용**만 하시면 됩니다!! 

수고하셨습니다.

**Express + MongoDB + GraphQL 끝.**

#### - 한 번 해보기
1. graphql-yoga : https://github.com/prisma/graphql-yoga
2. 데이터 값 바꿔가며 해보기
3. index.js => 'dbName' 변경 후 Create 해보고 MongoDB 확인해보기
4. 로컬로 MongoDB 사용 해보기

#### - 글을 쓰며 참고했던 곳
1. MongoDB Node.js Driver : https://docs.mongodb.com/ecosystem/drivers/node/
2. MongoDB 공식 홈페이지 : https://www.mongodb.com/
3. GraphQL 스키마&타입 : https://graphql-kr.github.io/learn/schema/
4. mongoose Quick start guide : https://mongoosejs.com/docs/index.html
5. graphiql Github : https://github.com/graphql/graphiql
6. Building APIs with GraphQL, NodeJs, and Mongoose : https://medium.com/@haidermalik504/building-apis-with-graphql-nodejs-and-mongoose-64655c062dd2
