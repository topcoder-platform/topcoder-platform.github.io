# Node.js Services Style Guide

Here are some libraries that we recommend using to achieve these patterns.

### Linter
Use [AirBnb ES Lint config](https://github.com/airbnb/javascript). Installation and setup steps are documented https://www.npmjs.com/package/eslint-config-airbnb.
Add the following rules to your .eslintrc:
```
{
  "extends": "airbnb",
  "parser": "babel-eslint",
  "env": {
    "browser": true,
    "node": true,
    "es6": true,
    "mocha": true
  },
  "rules": {
    "valid-jsdoc": ["error", {
      "requireReturn": true,
      "requireReturnType": true,
      "requireParamDescription": true,
      "requireReturnDescription": true
    }],
    "require-jsdoc": ["error", {
        "require": {
            "FunctionDeclaration": true,
            "MethodDefinition": true,
            "ClassDeclaration": true
        }
    }]
  }
}

```

### Configuration

Use [node-config](https://github.com/lorenwest/node-config) to externalize service configuration. Make sure NODE_ENV variable is set to `local`,  `development` or `production` for configuration of the appropriate environment to be picked up.
* _config/default.json_: contains default values accessible for all environments.
* _config/production.json_: Only add configuration settings that are required to be overwritten for *production* environment
* _config/development.json_: Only add configuration settings that are required to be overwritten for *development* environment
* _config/test.js / .json_: Configuration for test environment
* _config/local.js / .json_: Local configuration values. *NOTE* your local configuration should **not** be checked in to source control. Please make sure config/local.js/on should be added to gitignore.
* _config/custom-environment-variables.json_: Add custom environment variables. See https://github.com/lorenwest/node-config/wiki/Environment-Variables#custom-environment-variables for more information.

See https://github.com/lorenwest/node-config/wiki/Configuration-Files#file-load-order for file load order.

Sensitive data such as API keys and other secrets should NOT be checked into source code unencrypted. Either use [git-crypt](https://github.com/lorenwest/node-config/wiki/Securing-Production-Config-Files) or pass them via CI / CD process as environment variables.

### Logging

When it comes to using logging frameworks for Node.js, [bunyan](https://github.com/trentm/node-bunyan) and [winston](https://github.com/winstonjs/winston) are both very good. We chose to go with bunyan because of the ability to create child loggers. Our log helper library [tc-core-library-js](https://github.com/appirio-tech/tc-core-library-js) attaches a child logger to express4 request object. This child logger is provided the request Id. All log entries from the child logger will be annotated with the requestId. This is very useful when debugging requests in production env. tc-core-library-js also supports piping data to [Logentries](https://logentries.com) provided LOGENTRIES_TOKEN and CAPTURE_LOGS environment variables are set.

Code snippet to use tc-core-library-js to setup the logger:
```
// Define and configure app
var app = express();

// add request Id
var addRequestId = expressRequestId();
app.use(addRequestId);

// init logger
var logger = coreLib.logger({
  name: 'SampleApp',
  level: _.get(config, "logLevel", 'debug').toLowerCase(),
  captureLogs: config.get('captureLogs'),
  logentriesToken: _.get(config, 'logentriesToken', null),
});
app.use(coreLib.middleware.logger(null, logger));
app.logger = logger;

```
