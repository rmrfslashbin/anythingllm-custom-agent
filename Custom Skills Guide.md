# AnythingLLM Custom Skills

## Introduction to custom agent skills
AnythingLLM allows you to create custom agent skills that can be used to extend the capabilities of your @agent invocations. These skills can be anything you want from a simple API call to even operating-system invocations. The sky is the limit! Depending on how you run AnythingLLM, you can create custom agent skills that can run extra processes like running a local Python script or, on Desktop, even operating-system invocations. If it can be done in NodeJS, it can likely be done in AnythingLLM.

## The current state of custom agent skills
Custom agent skills are newly supported in AnythingLLM and may have some bugs, quirks, missing features, unsupported features, etc. Please report any feature requests or bugs you find to the GitHub repository (https://github.com/Mintplex-Labs/anything-llm).

## High Level Requirements
- NodeJS programming experience is required to create custom agent skills. Go to the developer guide to get started.
- Custom agent skills must exactly match the requirements listed on this help page.
- There are built in functions and utilities to help you log data or thoughts for an agent.
- There is currently no established tooling for creating custom agent skills - so follow this guide if developing skills for AnythingLLM.
- All skills must return a string type response - anything else may break the agent invocation.

## Availability
Custom agent skills are available in the Docker image since commit d1103e or release v1.2.2. Custom agent skills are available in AnythingLLM Desktop version 1.6.5 and later. Custom agent skills are not available in the AnythingLLM Cloud offering.

## How to develop custom agent skills
This guide is intended for developers who want to create custom agent skills for AnythingLLM.

## Prerequisites
- NodeJS 18+
- Yarn
- AnythingLLM running in some supported environment see here for more information.


## Guidelines for creation of custom agent skills
- Custom agent skills must be written in JavaScript and will execute within a NodeJS environment.
- You can bundle any NodeJS package you want within your custom agent skill, but it must be present in the folder structure of your custom agent skill.
- All functions must return a string value, anything else may break the agent invocation.
- You should provide a `README.md` file at the root of your custom agent skill with a description, any additional requirements and how to use the custom agent skill.
- You must define your plugin with an associated `plugin.json` file at the root of your custom agent skill folder.
- The must define your entry point of your custom agent skill as a `handler.js` file.
- You must wrap your entire custom agent skill in a folder with the same name property that is defined in the `plugin.json` file.


## Hot loading of custom agent skills
If you are in an active agent invocation when you make changes to your custom agent skill, you will need to /exit the current session for the changes to take effect. If you just added a new custom agent skill you will need to revisit or reload the page for the new skill to be shown in the UI. AnythingLLM supports hot loading of custom agent skills. This means that you can make changes to your custom agent skill and see the changes without having to restart the agent or the instance of AnythingLLM.

## Where to place your custom agent skill code
All agents skills must be placed in the appropriate folder in your AnythingLLM storage directory folder. This can be found in multiple locations depending on the environment you are running AnythingLLM in. In all versions you are looking for the matching folder of the STORAGE_DIR environment variable. Your entire custom agent skill folder should be wrapped in a folder with the same hubId property as the associated `plugin.json` file.

### Local Development
When running AnythingLLM locally, your storage directory is likely mounted in the `server/storage` directory. Then you will need to create this subfolder within the storage directory: `plugins/agent-skills`.

### Desktop
When running AnythingLLM on Desktop, your storage directory can be found using this guide. Then you will need to create this subfolder within the storage directory: `plugins/agent-skills`.

### File structure
Your custom agent skill should be wrapped in a folder with the same hubId property that is defined in the `plugin.json` file. See the `plugin.json` reference for more information on the `plugin.json` file, its properties and how to use them.

```
// example `plugin.json`
{
  "name": "This is my human readable name",
  "hubId": "my-custom-agent-skill" // THIS MUST BE THE SAME AS THE parent folder name. Can be any string.
}
```

Folder structure for associated agent skill: NOTE: The folder name must match the hubId property in the `plugin.json` file.

```
plugins/agent-skills/my-custom-agent-skill
|-- `plugin.json`
|-- `handler.js`
|-- // You can add any additional files you want to the folder and reference them in the `handler.js` file!
```

## plugin.json reference
This page is intended for developers who want to create custom agent skills for AnythingLLM.


The `plugin.json` file is used to define a custom agent skill for AnythingLLM. It is a JSON file that contains the following properties:

```
{
  // see #active for more information
  "active": true,
 
  // see #hubId for more information
  "hubId": "open-meteo-weather-api",
 
  // see #name for more information
  "name": "Get Weather",
 
  // see #other_properties for more information
  "schema": "skill-1.0.0",
  "version": "1.0.0",
  "description": "Gets the weather for a given location latitude and longitude using the open-meteo API",
  "author": "@tcarambat",
  "author_url": "https://github.com/tcarambat",
  "license": "MIT",
 
  // see #setup_args for more information
  "setup_args": {
    "OPEN_METEO_API_KEY": {
      "type": "string",
      "required": false,
      "input": {
        "type": "text",
        "default": "YOUR_OPEN_METEO_API_KEY",
        "placeholder": "sk-1234567890",
        "hint": "The API key for the open-meteo API"
      },
    }
  },
 
  // see #examples for more information
  "examples": [
    {
      "prompt": "What is the weather in Tokyo?",
      "call": "{\"latitude\": 35.6895, \"longitude\": 139.6917}"
    },
    {
      "prompt": "What is the weather in San Francisco?",
      "call": "{\"latitude\": 37.7749, \"longitude\": -122.4194}"
    },
    {
      "prompt": "What is the weather in London?",
      "call": "{\"latitude\": 51.5074, \"longitude\": -0.1278}"
    }
  ],
 
  // see #entrypoint for more information
  "entrypoint": {
    "file": "handler.js",
    "params": {
      "latitude": {
        "description": "Latitude of the location",
        "type": "string"
      },
      "longitude": {
        "description": "Longitude of the location",
        "type": "string"
      }
    }
  },
 
  // see #imported for more information
  "imported": true
}
```

### Fields

#### active
The `active` property is a boolean that determines if the custom agent skill is active. If it is set to `false`, the custom agent skill will not be loaded.

#### name
The `name` property is a string that is used to identify the custom agent skill. This is the human-readable name of the skill that is displayed in the AnythingLLM UI.

#### hubId
The `hubId` property is a string that is used to identify the custom agent skill. This must be the same as the parent folder name.

#### other_properties
The `other_properties` property is a list of other properties that are used to define the custom agent skill. These are mostly optional and will not impact performance of the skill directly. See reference below for more information.
```
{
  "schema": "skill-1.0.0", // REQUIRED - do not change
  "version": "1.0.0", // REQUIRED - can be defined by user
  "description": "short description of the custom agent skill", // REQUIRED
  "author": "@tcarambat", // OPTIONAL - author tag of the custom agent skill
  "author_url": "https://github.com/tcarambat", // OPTIONAL - url of the author of the custom agent skill
  "license": "MIT" // OPTIONAL - license of the custom agent skill
}
```

#### setup_args
Setup arguments are used to configure the custom agent skill from the UI and make runtime arguments accessible in the handler.js file when the skill is called. The key of the setup argument is the name of the argument that is used in the handler.js file, while its properties automatically generate the UI and inputs for the argument in the AnythingLLM UI.
```
"setup_args": {
    "OPEN_METEO_API_KEY": {
      "type": "string", // What type of value is expected
      "required": false, // Is the argument required
      // Defines the UI of the input to be rendered in the AnythingLLM UI
      "input": {
        "type": "text", // What type of input to be rendered
        "default": "YOUR_OPEN_METEO_API_KEY", // Default value of the input
        "placeholder": "sk-1234567890", // Placeholder text for the input
        "hint": "The API key for the open-meteo API" // Hint text for the input
      },
      "value": "" // (optional) preset value of the argument - will be replaced by the user input in the AnythingLLM UI, but can be hardcoded.
    }
  },
  ```

#### examples
The `examples` property is a array of examples that are used to pre-inject examples into the custom agent skill. These are optional but highly encouraged as providing some expected examples helps LLMs determine the more "use-case" oriented implementation of the skill. Try to provide anywhere from 1-3 examples that are relevant to the skill as these are injected into the prompt and can help guide the LLM in the correct direction.

The `call` property should match the expected input format of the custom agent skill in the `handler.js` file.
```
// handler.js
module.exports.runtime = {
  // latitude and longitude are the expected parameters for the custom agent skill
  handler: async function ({ latitude, longitude }) {
    // ... do something with latitude and longitude
  },
};
```

```
"examples": [
  // Example prompts and expected invocation format for the custom agent skill
  // these are optional but highly encouraged since they help the LLM understand the expected format of the custom agent skill
  // and when to use the associated skill with respect to the user prompt.
  // This is known as "few-shot prompting" and is a best practice when creating custom agent skills.
  {
    "prompt": "What is the weather in Tokyo?",
    "call": "{\"latitude\": 35.6895, \"longitude\": 139.6917}"
  },
  {
    "prompt": "What is the weather in San Francisco?",
    "call": "{\"latitude\": 37.7749, \"longitude\": -122.4194}"
  },
  {
    "prompt": "What is the weather in London?",
    "call": "{\"latitude\": 51.5074, \"longitude\": -0.1278}"
  }
]
```

#### entrypoint
The `entrypoint` property is used to define the entrypoint of the custom agent skill and the expected inputs! This is the file location and invocation parameters that are used to execute the custom agent skill.
```
"entrypoint": {
  "file": "handler.js", // location of the file to be executed with respect to the plugin.json file
  "params": {
    // all properties require a description and type and should match the expected input format of the custom agent skill in the handler.js file
    "latitude": {
      "description": "Latitude of the location", // Short description of the parameter purpose
      "type": "string" // supported types: string, number, boolean
    },
    "longitude": {
      "description": "Longitude of the location",
      "type": "string"
    }
  }
}
```

#### imported
The `imported` value must be set to true.

## handler.js reference
This page is intended for developers who want to create custom agent skills for AnythingLLM.

### Rules & Guidelines
- The handler.js file must export a runtime object with a handler function.
- The handler function must accept a single argument which is an object containing the parameters defined in the plugin.json entrypoint property, if any.
- The handler function must return a string value, anything else may break the agent invocation or loop indefinitely.
- You must use require to import any modules you need from the NodeJS standard library or any modules you have bundled with your custom agent skill.
- You must use await when making any calls to external APIs or services.
- You must wrap your entire custom agent skill in a try/catch block and return any error messages to the agent at invocation time.

### Available runtime properties and methods
#### this.runtimeArgs
The `this.runtimeArgs` object contains the arguments that were passed to the `setup_args` from the `plugin.json` file.

You can access the value of a specific argument by using the `propertyName` as the key.
```
// plugin.json excerpt
// "setup_args": {
//     "OPEN_METEO_API_KEY": {
//       "type": "string",
//       "required": false,
//       "input": {
//         "type": "text",
//         "default": "YOUR_OPEN_METEO_API_KEY",
//         "placeholder": "sk-1234567890",
//         "hint": "The API key for the open-meteo API"
//       },
//       "value": "sk-key-for-service",
//     }
//   },
 
this.runtimeArgs["OPEN_METEO_API_KEY"]; // 'sk-key-for-service'
```

#### this.introspect
The `this.introspect` function is used to log "thoughts" or "observations" to the user interface while the agent is running.
```
this.introspect("Hello, world!"); // must be a string - will be shown to user
```

#### this.logger
The `this.logger` function is used to log messages to the console. This is useful for debugging your custom agent skill via logs.
```
this.logger("Hello, world!"); // must be a string - will be printed to console while the agent is running
```

#### this.config
The `this.config` object contains the configuration for your custom agent skill. Useful for when you need to know the name of your custom agent skill or the version or for logs.
```
this.config.name; // 'Get Weather'
this.config.hubId; // 'open-meteo-weather-api'
this.config.version; // '1.0.0'
```

### Example handler.js
Objective: Get the weather for a given location latitude and longitude using the open-meteo API.
```
// handler.js
// NOT RECOMMENDED: We're using an external module here for demonstration purposes
// this would be a module we bundled with our custom agent skill and would be located in the same folder as our handler.js file
// Do not require modules outside of the plugin folder. It is recommended to use require within a function scope instead of the global scope.
// const _ExternalApiCaller = require('./external-api-caller.js');
 
module.exports.runtime = {
  handler: async function ({ latitude, longitude }) {
    const callerId = `${this.config.name}-v${this.config.version}`;
    try {
      this.introspect(
        `${callerId} called with lat:${latitude} long:${longitude}...`
      );
      const response = await fetch(
        `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true&hourly=temperature_2m,relativehumidity_2m,windspeed_10m`
      );
      const data = await response.json();
      const averageTemperature = this._getAverage(data, "temperature_2m");
      const averageHumidity = this._getAverage(data, "relativehumidity_2m");
      const averageWindSpeed = this._getAverage(data, "windspeed_10m");
      return JSON.stringify({
        averageTemperature,
        averageHumidity,
        averageWindSpeed,
      });
    } catch (e) {
      this.introspect(
        `${callerId} failed to invoke with lat:${latitude} long:${longitude}. Reason: ${e.message}`
      );
      this.logger(
        `${callerId} failed to invoke with lat:${latitude} long:${longitude}`,
        e.message
      );
      return `The tool failed to run for some reason. Here is all we know ${e.message}`;
    }
  },
  // Helper function to get the average of an array of numbers!
  _getAverage(data, property) {
    return (
      data.hourly[property].reduce((a, b) => a + b, 0) /
      data.hourly[property].length
    );
  },
 
  // Recommended: Use this method to call external APIs or services
  // by requiring the module in the function scope and only if the code execution reaches that line
  // this is to prevent any unforseen issues with the global scope and module loading/unloading.
  // This file should be placed in the same folder as your handler.js file.
  _doExternalApiCall(myProp) {
    const _ScopedExternalCaller = require("./external-api-caller.js");
    return _ScopedExternalCaller.doSomething(myProp);
  },
};
```

## Where is my data located?
All data pertaining to AnythingLLM Desktop will be in the following locations. Please replace `<usr>` with your device username.

- On Mac: `/Users/<usr>/Library/Application Support/anythingllm-desktop/storage`
- On Linux: `/Users/<usr>/.config/anythingllm-desktop/storage/`
- On Windows: `C:\User\<usr>\AppData\Roaming\anythingllm-desktop\storage`

### What is each folder?
- `lancedb`: This it where your local vector database and its tables are stored.
- `documents`: This is the parsed document content of any uploaded files.
- `vector-cache`: This folder is the cached and embedded representation of a previous uploaded and embedded file. Its filename is hashed.
- `models`: Any locally stored LLMs or Embedder models used by the system are stored here. Typically are GGUF files.
- `anythingllm.db`: This is the AnythingLLM SQLite database.
- `plugins`: This is the folder where your custom agent skills are stored.