# Purpose

The AWS Amplify CLI provides decent out-of-the-box resolvers for DynamoDB backends that support use filtering 
listItems() API calls. As best I can tell, the AWS Amplify CLI does **not** provide the same level of support 
when creating an API from an existing Aurora Serverless data source. So, this project is my attempt to create
flexible request and response resolvers for a Aurora Serverless MySQL backend. 

# Note

For now, I'm periodically updating this repo with resolvers I'm creating for a specific project, related to
service demos. At a later time, I would like to run through and rename columns / tables to give them a more
generic use case or class name. 

# Current Functionality

## Requests

Requests support: 

* **nextToken** - optional INT field that dicates the item from which the list query should begin. 
* **limit** - optional INT field determining how many items to return
* **sortOrder** - optional STRING of value DESC or ASC, indiciating sort order. Default is ASC
