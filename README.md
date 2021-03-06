# aws-automation-utils  

[![Build Status](https://travis-ci.org/camilin87/aws-automation-utils.svg?branch=master)](https://travis-ci.org/camilin87/aws-automation-utils)

Some Helpers to automate certain AWS tasks such as restoring an RDS instance or updating ElasticBeanstalk environment settings

## Usage  

```bash
npm i aws-automation-utils --save
```

## Create fresh backup of a database  

Spawns a new instance with the desired parameters. It waits for the instance to be available. The new instance has a unique name based on the current date and time of the day.  

```js
const rdsService = require('aws-automation-utils').rds;
const newDatabaseName = await rdsService.restore({
    enabled: true,
    region: 'us-east-1',
    timeout: 600,
    prod: {
        dbIdentifier: 'production-database'
    },
    dev: {
        databasePrefix: "dev-database",
        securityGroups: ["sg-123abc"],
        tags: [ {
            "Key": "db_type",
            "Value": "dev"
        }],
        availabilityZone: 'us-east-1c',
        instanceClass: 'db.t2.small',
        publiclyAccessible: true,
        multiAZ: false
    }
});
console.log(`Created database: ${newDatabaseName}`);
```

## Change the Master Password of a database  

Changes the master password of a database. It waits for the instance to be available.  

```js
const rdsService = require('aws-automation-utils').rds;
await rdsService.updatePassword({
  enabled: true,
  region: 'us-east-1',
  timeout: 600,
  dbIdentifier: 'dev-database-2019-02-24',
  password: 'SUPER_SECRET'
});
```

## Delete old Development databases  

Deletes old database instances based on their name. As long as they start with the specified prefix.  

```js
const rdsService = require('aws-automation-utils').rds;
await rdsService.deleteOlder({
  enabled: true,
  region: 'us-east-1',
  databasePrefix: 'dev-database'
});
```

## Update an Elastic Beanstalk environment variable  

Sets an environment value in the specified Elastic Beanstalk environment. It waits for the environment to be ready.  

```js
const elasticBeanstalkHelper = require('aws-automation-utils').elasticBeanstalk;
await elasticBeanstalkHelper.updateEnvironment({
  enabled: true,
  region: 'us-east-1',
  environmentName: 'dev-environment',
  key: 'API_URL',
  value: 'https://myapi.com/api/v1/',
  timeout: 600
});
```

## Set a Lambda Environment Variable  

Sets an environment value in the specified lambda function. It creates a new setting if the setting does not exist.  

```js
const lambda = require('aws-automation-utils').lambda;
await lambda.updateEnvironment({
    region: 'us-east-2',
    functionName: 'function1',
    key: 'TEST',
    value: 'MAYBE'
});
```

## Disable a lambda  

Disables all the event source mappings of the specified lambda function.  

```js
const lambda = require('aws-automation-utils').lambda;
const statusUpdateResult = await lambda.updateStatus({
    region: 'us-east-2',
    functionName: 'function1',
    enabled: false
});
```

## Enable a lambda  

Enables all the event source mappings of the specified lambda function.  

```js
const lambda = require('aws-automation-utils').lambda;
const statusUpdateResult = await lambda.updateStatus({
    region: 'us-east-2',
    functionName: 'function1',
    enabled: true
});
```

## Change the concurrency limit of a lambda  

Sets the concurrency limit of a lambda to the specified value. Disables the limit when `0` is specified.  

```js
const lambda = require('aws-automation-utils').lambda;
const statusUpdateResult = await lambda.updateConcurrency({
    region: 'us-east-2',
    functionName: 'function1',
    concurrency: 10
});
```

## Disable the concurrency limit of a lambda  

```js
const lambda = require('aws-automation-utils').lambda;
const statusUpdateResult = await lambda.updateConcurrency({
    region: 'us-east-2',
    functionName: 'function1'
});
```

