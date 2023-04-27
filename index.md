# EverscaleDocumentation


## **Little about Ever platform**

Ever Platform provides developers with products, APIs and tools, that help build, test and run applications on TVM-compatible networks (Everscale, The Open Network (TON), Venom, Gosh, etc).Ever Platform provides developers with products, APIs and tools, that help build, test and run applications on TVM-compatible networks (Everscale, The Open Network (TON), Venom, Gosh, etc).
APIs are in graphQL query Language that are scalable, fast and provides smooth development flow from testing to production.
What is Evercloud?

It is a one of the tools provided by Ever platform. Evercloud basically provides TVM network developers with scalable graphQL APIs.
The APIS provided by Evercloud let you query data from any TVM blockchain. 
The APIs are provided for the following networks:
Everscale Mainnet
Everscale Devnet (stable network for development)
Everscale Testnet (FLD) (early network feature testing) 
The Open Network (TON) Mainnet 
Venom Testnet 
SOON - GOSH network

 
## **How to use Evercloud APIs?**

For that first we need the API key and API secret from Evercloud platform. To get the both, we need to follow these steps: 

**Step 1**
Go to Evercloud dashboard by simply clicking on the following link: https://dashboard.evercloud.dev/
You will be redirected to this page 

 ![image](https://user-images.githubusercontent.com/52127829/234943867-03214c12-4f48-45d7-9482-e8c2520087d5.png)


Simply enter your details and create your account.

**Step 2**
Once you have created an Account, you will be redirected to you Evercloud Dashboard. You will see an interface like below

![image](https://user-images.githubusercontent.com/52127829/234944041-20d30392-7cc9-4510-b15b-e5b8b59890c2.png)

Click to create Add project button from the side bar and enter the name of the project you want to create.

![image](https://user-images.githubusercontent.com/52127829/234944090-7c69c259-4e52-4503-aa63-d593a6ea8c01.png)

![image](https://user-images.githubusercontent.com/52127829/234944158-9f054b7a-2e1e-4188-95fd-da9a54eb5d19.png)

**Step 3**
Once you have a project, click on the Project Name that you created from the sidebar.


![image](https://user-images.githubusercontent.com/52127829/234944281-0c9f8e40-7eac-47d8-86e3-d204e0562c25.png)


You will see dashboard like this  there you can see your graphQL endpoints that you can use to query the data for your application.

**Step 4**
Now go to the security sections from the navigation bar and you will see the page like this

![image](https://user-images.githubusercontent.com/52127829/234947989-f5739fc7-d83e-424c-a1aa-8d8f523b744a.png)

There are your API keys and API secret that you would require accessing the data through graphQL endpoints.
Copy both of them and save them somewhere safe in your code (use .env file for that in nodeJs project).

**Step 5:**
Now again move back to endpoints and click on the pink button as you see in the image below:

![image](https://user-images.githubusercontent.com/52127829/234948084-6317db90-ceca-405f-a2c6-bfccbccf51be.png)


you will be redirected to the following page: 

![image](https://user-images.githubusercontent.com/52127829/234948200-f8ecba0c-5e76-448a-8187-7d4c5925e3c3.png)


This is your personal graphQL playground that you can use to play with queries and access the data without needing anything else.
All you have to do is write graphQL query in the playground and start playing with the data.

## Example Query

Let’s create an example query and access the data in our graphQL playground. We will query the transactions data from the blockchain. And for that this is the query we have to write:
```

query GetBlockData{
  blockchain{
    transactions{
      pageInfo{
        endCursor
      }
      edges{
        cursor
        node{
          id
          block_id
          status_name
        }
      }
    }
  }
}
```

Copy the query and paste it in your graphQL playground. But this is not it. We need to provide the playground our API key to fetch the data and this is how we will do this.

## Setting API key in playground

In your playground you will see this tab in the bottom:

![image](https://user-images.githubusercontent.com/52127829/234948729-e96aa948-53a2-450e-9dfd-2ef73b4bb8c3.png)

Click on it and go to HTTP HEADERS and write this in line in the tab
```
{
  "Authorization" : "Bearer <API key>"
    }
```

And you are good to go.
Then simply click on the run button on the playground and you will see the data as in the image below on right side

![image](https://user-images.githubusercontent.com/52127829/234949347-04597908-ca1b-4703-89e9-4583c1fa8ea5.png)

## Example with GraphQL codegen

Now let’s see an example with GraphQL Codegen. The GraphQL codegen provides types for our queries so that we can get type safety in our code. To implement this first we need to create a nodeJs project. We will also require installing some dependencies in our project. To create an empty nodeJs project. Follow these steps: 

**Step 1**
Create a folder at any place of your choice and open it in your code editor. I will use VS code for this. Then open your terminal and write this command: 

```
npm init –y
```


This will create an empty project in the folder. Now we need to install some dependencies. These are: 

```npm install graphql @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations graphql-request @eversdk/core @eversdk/lib-node everdev ts-node```

Copy the above code an paste it in terminal. In this example we are not installing anything as a dev dependency as it is confusing for beginner. But if you know about this then only install **graphQL**,** @eversdk/core**, **@eversdk/lib-node** and **graphQL-request** as a project dependency. All other dependencies will be dev-dependencies.

**Step 2**
Now we will create a smart contract and deploy it on Everscale network. If you want to learn how to do this go to this link: https://docs.everscale.network/develop/tutorial/getting-started
Just follow each steps and you will have an example project. Also made changes according to your need. Here is our smart contract that we deployed to the Everscale network: 
```

pragma ton-solidity >= 0.67.0;
pragma AbiHeader expire;

contract Wallet {

    event TokenReceived(address indexed sender, uint256 indexed amount);
    event TokenPaid(address indexed receiver, uint128 indexed amount);

    constructor() {
        require(tvm.pubkey() != 0, 101);
        require(msg.pubkey() == tvm.pubkey(), 102);
        tvm.accept();
    }

    function receiveTokens() external payable {
        require(msg.pubkey() == tvm.pubkey(), 102);
        tvm.accept();
        emit TokenReceived(msg.sender, msg.value);
    }

    function sendValue(address dest, uint128 amount, bool bounce) public view {
        require(msg.pubkey() == tvm.pubkey(), 102);
        tvm.accept();
        dest.transfer(amount, bounce, 0);
        emit TokenPaid(dest, amount);
    }

    function getBalance() public returns(uint256){
        return address(this).balance;
    }
}
```

This is simple wallet that provide the functionality to to sendToken and recieveToken.
Now if you have followed the above tutorial, you will have a deployed contract address.
**Here is mine : 0:e245694a418cd496fe6913bfbcb6e5d2c9bff5a337219190be46ff7e11bcde0d**

We will make a call to this contract and query data from this contract through our contract. Go ahead and make some transactions with your smart contract. The steps to do that are also given in the above link.

**Step 3**
Next step is to use graphQL to make call to the contract.
This is the graphQL query that we made:
```

query GetTransactionData{
            blockchain{
                  account(address:"0:e245694a418cd496fe6913bfbcb6e5d2c9bff5a337219190be46ff7e11bcde0d"){
                transactions{
                  edges{
                    node{
                      id
                      destroyed
                      hash
                    }
                  }
                }
              }
            }
          }
```

The query above will fetch all transactions data from the smart contract address passed as an argument to account.

**Step 4**
Go to your code editor and create a file name Queries.ts at the root of your project. This file will hold all of our queries. In this file copy the above query like this:
```

import {gql} from "graphql-request";

export const GET_TRANSACTION_DATA = gql`
        query GetTransactionData{
            blockchain{
                  account(address:"0:e245694a418cd496fe6913bfbcb6e5d2c9bff5a337219190be46ff7e11bcde0d"){
                transactions{
                  edges{
                    node{
                      id
                      destroyed
                      hash
                    }
                  }
                }
              }
            }
          }`;
```

**Step 5**
Now we need to create codegen.ts or codegen.yml file that will tell the @graphql-codegen/cli (tool that we installed using npm in the begging) where to look for files that we require typescript definitions for. It will also contains some other important informations like where would the generated types will be stored and graphQL schema etc. Go ahead create this file at the root of your project and paste this code: 
For Typescript file
```

import { CodegenConfig } from '@graphql-codegen/cli';

const config: CodegenConfig = {
  overwrite: true,
  schema: 'https://devnet.evercloud.dev/<ProjectId>/graphql',
  documents: './Queries.ts',
  generates: {
    'src/generated/graphql.ts': {
      plugins: [
        'typescript',
        'typescript-operations',
      ],
    },
  },
};

export default config;
```

In the above code, add your project id at the place of **<ProjectId> (can be found on evercloud dashboard)**. Keep everything as it is. If you are familiar with this, then you can use more complex config file.

Step 6
Now we have everything set for generating types for our project. Now Go to your **package.json** file and add this command under scripts:
```

{
  "name": "everdev",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "codegen": "graphql-codegen-esm --config codegen.ts"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@eversdk/core": "^1.42.1",
    "@eversdk/lib-node": "^1.42.1",
    "everdev": "^1.6.1",
    "graphql": "^16.6.0",
    "graphql-request": "^6.0.0",
    "ts-node": "^10.9.1"
  },
  "devDependencies": {
    "@graphql-codegen/cli": "^3.3.1",
    "@graphql-codegen/typescript": "^3.0.4",
    "@graphql-codegen/typescript-operations": "^3.0.4"
  }
}

```

Now we have everything. Next you need to run this command in your terminal for generating types: 
```
npm run codegen
 
 ```


After running this command you should see this on terminal:
 
 
 ![image](https://user-images.githubusercontent.com/52127829/234950334-07f62ff1-ec45-47ce-ba99-c55057dcc565.png)


This means that we have done everything correctly. Now go ahead and check src/generated folder. You will see this:
 <img width="468" alt="image" src="https://user-images.githubusercontent.com/52127829/234950358-4e3a1d05-cfb2-40e0-9997-3ba0b3669a9a.png">


This is the file that gets generated after running the command and will contains all our necessary types. Open it and you will see this big file: 
 
 ![image](https://user-images.githubusercontent.com/52127829/234950413-67ff09af-e0d5-4c1a-a9fb-f2f34d3d6619.png)

 
Move to bottom and you will see your query. Mine is like this:
 
 ![image](https://user-images.githubusercontent.com/52127829/234950427-6580d269-589c-4098-abfe-c59efab28bb1.png)


As you can see we got these types all thanks to graphQL codegen.
**Note: If you want to add more queries to Queries.ts file, go ahead and add it. Then you need to run ‘npm run codegen’ again to generate the types for this query as well.**

**Step 7**
Now let’s make the actual query. Go ahead and create a file name index.ts in src folder and paste the following code:
```
 import { GET_TRANSACTION_DATA } from '../Queries';
import {request} from 'graphql-request'
import {GetTransactionDataQuery} from "./generated/graphql";
  const url = "https://devnet.evercloud.dev/223ed117967a44f9ad7a5f01f67d04da/graphql"

  
async function main() {
    request<GetTransactionDataQuery>(url, GET_TRANSACTION_DATA,{},{
      "X-API-KEY": `<API KEY> `
    }).then(data=>{
      console.log(data.blockchain?.account?.transactions?.edges);
    }).catch(err=>{
      console.log(err);
    })
  } 

  main()

```
In the above code, add your API secret at the place of <API KEY>. Here we are importing our query GetTransactionDataQuery from the generated types file to make the request we are making will return the fields as mentioned in the query.
Now we are making query with the help of request function of the “graphql-request” and passing ‘GetTransactionDataQuery` as generic to get the returned data in the form of this.
And you will also see that your code editor will give you these beautiful types recommendations that was not possible before that: 

 ![image](https://user-images.githubusercontent.com/52127829/234950697-9a855582-df35-4536-829c-248876311819.png)

**Step 8**
Now we need to run the query. To do that run the following command in the terminal: 

```npx ts-node src/index.ts```
 
Now you should get back response in the terminal like this:


And this is it. Now try to make some more queries according to your need and try to test it.
