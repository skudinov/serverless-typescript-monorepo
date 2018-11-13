# Serverless starter project with babel 7, typescript and yarn workspaces

This starter project is designed to get you up and running with [serverless](https://serverless.com/) project 
that contains multiple services under the same shared API Gateway endpoint.

It uses `webpack` `babel 7` and `@babel/preset-typescript` for typescript support.   
You'll have `jest` tests out of the box

## Reasons
In a big project at some point it is nessesery to split logic into multiple stacks and deploy as separate services.  
It gives further separation of concerns and allows to update individual stacks separatly.   

This project has a [main service](serverless.yml) that holds only shared resources that can be reused across the services
like dynamoDB tables and APIGateway resources and functional services, like [users](src/users/serverless.yml),


that hold business logic. This approach not only allows to easely add and remove individual services but also
reuse existing resources for stages. It is very usefull if you have existing dynamoDB tables in production.

## Configuration
Change [config.yml](config.yml) to reflect your configurations

#### 1. Install serverless framework
`yarn global add serverless`

#### 2. Install dependencies

In the root folder run `yarn install` 

#### 3. Deploy of the resources stack

In the root folder run `sls deploy -s <DEV/PROD> --aws-profile=<AWS_PROFILE>`

#### 4. Deploy functions stacks

- `cd src/<SERVICE>`
- `sls deploy -s <DEV/PROD> --aws-profile=<AWS_PROFILE>`

## Debugging

Stream all logs for a specific Function from CloudWatch 

 - `cd src/<SERVICE>`
 - `sls logs -t -s <DEV/PROD> -r <REGION> --aws-profile=<AWS_PROFILE> -f <FUNCTION_NAME>`
 
## Folder stucture

Each lambda function has a dedicated folder inside the service with a descriptive name and uses key words for 
CRUD endpoints like `create`, `update`, `get`, `list`, `delete`, for example [createUser](src/users/createUser)
 
Each folder contains a [handler.ts](src/users/createUser). As a convention handler should not contain any business logic, 
it only uses middlewares for common logic like event validation, error handing etc. 
We use [middy](https://github.com/middyjs/middy) middleware engine for that purpose.

Add your business logic goes to the file with the same name as a folder like 
[createUser.ts](src/users/createUser/createUser.ts). This provides a better autocompition for ides.
 
## Recommendations

Explicitly pass all required parameters to your function from the event so it is clear what parameters are used. 
It is also easier to change mapping in the future in case if you want to use the same function as an AppSync resolver.

If you have incoming body, pathParameters or queryParameters - use [eventValidator](src/common/middlewares/eventValidator.ts) 
middleware to validate them. Your handler will look like [this](src/notes/createNote/handler.ts)
