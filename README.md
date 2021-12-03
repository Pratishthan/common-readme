

# oepy-common

Common repo for payment rules engine

To enable these models add the following entry into app-list.json

  {
    "path":"oepy-common",
    "enabled":true
  },
  {
        "path": "oe-local-cache",
        "enabled": true
  },
Also make sure there is a datasource with the name db. To use paymentRules add storage datasource as mentioned below. Add module dependency for oe-local-cache.

Payment Rule
This oe-cloud module implements the business rule functionality. The most common way to represent a rule is via payment tables. This module extensively makes use of js-feel business rule engine. Payment tables are written in excel files, and uploaded to the PaymentTable model through an api /api/PaymentRuleContainers/uploadFile

Rules for writing making excel sheet

Rule 1-Must Take care of the hit policy

1 Unique – only a single rule can be matched, make sure no two inputs can have same value

2 Any – multiple rules can match, but they all have the same output

3 Priority – multiple rules can match, with different outputs. The output that comes first in the supplied output values list is returned.when there is two output columns, the first column is given more priority than the second column.

4 First – return the first match in rule order .In this if multiple input match then output should be the first rule.

5 Collect – return a list of the outputs in arbitrary order

  C+ – return the sum of the outputs
  C# – return the count of the outputs
  C< – return the minimum-valued output
  C> – return the maximum-valued output
6 Rule order – return a list of outputs in rule order

7 Output order – return a list of outputs in the order of the output values list

Rule 2-when any input is in string do not forget to enclosed in double quotes

Rule 3-Make sure there is at least two entries in the the table

Rule 4-make sure columns are properly merged

Rule 5-In an output we cannot give empty string instead of empty string use null or undefined

Rule 6-while creating the table make sure no extra column and row remain outside the table

Rule 7-Inputs are case sensitive These rules must be followed in order to make excelFile

For storing this file we must create a local storage

Step 1-Add these lines in datasources.json

"pg_file": {
  "name": "pg_file",
  "connector": "loopback-component-storage-postgres",
  "host": "localhost",
  "port": 5432,
  "database": "test",
  "table": "filestorage",
  "username": "test-user",
  "password": "test-password"
}
And run this commnad
  CREATE TABLE "filestorage" (
    id SERIAL PRIMARY KEY,
    container TEXT NOT NULL,
    filename TEXT NOT NULL,
    mimetype TEXT,
    objectid INTEGER UNIQUE NOT NULL
  );
Step 2- Put a rule service configuration in your config.json if your use case requires to access any api’s

       {
         "serviceId":"12",
         "serviceMeta":    {
           "field": "key2",
           "method": {
               "type": "API",
               "httpMethod": "GET",// Only GET is supported.
  "url": "host:port?queryParam1=qp1&queryParam2=qp2"
               “query”:”” 
           }
       }
       }
   ]
Step 3-Make a new folder name 'PaymentRule' in your root directory

Step 4-Invoke this api /api/PaymentRuleContainers/uploadFile through postman and attach the file in the form-data in the body and give the query parameter bankId, ruleId and ruleType

# For execution
invoke paymentRules model.execute function of paymentRules model and give bankId,ruleType,ruleId and payload as an argument

Node-red node
There is a node-red node, for using that in your node-red flow, first include the path of the nodes folder in the node-red-settings.js Something like this

nodesDir: ['nodes','node_modules/oepy-common/nodes']

# Logging
Include this in your environment for enabling logging.

export LOGGER_CONFIG='{"logStreams":[{"type":"file","path":"./a.log","level":"info"}],"levels":{"default":"info"},"enableContextLogging":1}'
