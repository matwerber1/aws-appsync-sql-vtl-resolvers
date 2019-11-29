# Purpose

Provide generic AWS AppSync Resolvers to interact with an Aurora MySQL Serverless backend data source. 

The focus will first be on flexible ListItems() calls, since these are more complex. Atomic / single-item gets, deletes, updates are not a focus right now. 

# Background

The AWS Amplify CLI provides decent out-of-the-box resolvers for DynamoDB backends that support use filtering 
listItems() API calls. As best I can tell, the AWS Amplify CLI does **not** provide the same level of support 
when creating an API from an existing Aurora Serverless data source. So, this project is my attempt to create
flexible request and response resolvers for a Aurora Serverless MySQL backend. 

# Note

For now, I'm periodically updating this repo with resolvers I'm creating for a specific project, related to
service demos. At a later time, I would like to run through and rename columns / tables to give them a more
generic use case or class name. 

# Disclaimer

I am new to writing VTL for AppSync. Please carefully review before relying on my work. 

# Thoughts

1. Sorting on a non-unique / non-primary key
 
 If sorting on a non-unique or non-primary key, subsequent paginated queries might request the same items again. We need to think of a way to make these types of queries deterministic. I think it might be as simple as just sorting by a combination of the user-specified non-primary key, plus the primary key. For example, if user specifies sort by create date, we will actually `ORDER BY CREATE_DATE, ID`, and in addition to returning a `NextToken: (next date)`, we will also return `NextId: = last-processed ID`

# Current Functionality

## ListItems

Requests support: 

* **nextToken** - optional INT field that dicates the item from which the list query should begin. 
* **limit** - optional INT field determining how many items to return.
* **sortOrder** - optional STRING of value DESC or ASC, indiciating sort order. Default is ASC.
* **sortKey** - optional STRING field that determines the single field by wich results are ordered; default is "id" field. 
* **filters** - an array of objects, where each object is translated to a WHERE clause, separated by ANDs
  * each object must contain:
    * **fieldName** - name of the field we are filtering on
    * **fieldType** - one of 'string', 'number'   (which impacts whether or not we enclose values in quotes)
  * each object contains one or more of: 
    * **eq** - a single value, resulting in `WHERE ${fieldName} = ${eq}`
    * **ne** - a single value, resulting in `WHERE ${fieldName} != ${ne}`
    * **le** - a single value, resulting in `WHERE ${fieldName} <= ${le}`
    * **lt** - a single value, resulting in `WHERE ${fieldName} < ${lt}`
    * **ge** - a single value, resulting in `WHERE ${fieldName} > ${ge}`
    * **gt** - a single value, resulting in `WHERE ${fieldName} >= ${gt}`
    * **in** - an array of values, resulting in `WHERE $fieldName IN ($in[0], $in[1], ...)`
