# AnythingLLM Custom Skills

## Introduction to custom agent skills
AnythingLLM allows you to create custom agent skills that can be used to extend the capabilities of your @agent invocations. These skills can be anything you want from a simple API call to even operating-system invocations. The sky is the limit! Depending on how you run AnythingLLM, you can create custom agent skills that can run extra processes like running a local Python script or, on Desktop, even operating-system invocations. If it can be done in NodeJS, it can likely be done in AnythingLLM.

## The current state of custom agent skills
Custom agent skills are newly supported in AnythingLLM and may have some bugs, quirks, missing features, unsupported features, etc. Please report any feature requests or bugs you find to the GitHub repository (https://github.com/Mintplex-Labs/anything-llm).

## High Level Requirements
- All JavaScript code must comply with CommonJS module standards (using require() and module.exports). ES Modules syntax (import/export statements) and ES Module file extensions (.mjs) are not permitted. This includes avoiding package.json "type": "module" configurations. This standardization ensures consistent module loading behavior across the project.
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

### Development Best Practices
- Include a complete development environment setup including:
  - ESLint configuration (`.eslintrc.js`)
  - Jest test configuration
  - Example test files
  - Basic error handling templates
- Provide clear error messages and appropriate fallback behaviors
- Include handling for edge cases (e.g., non-English characters, missing data)
- Log meaningful debug information using the provided `introspect` and `logger` functions

### Error Handling
Custom agent skills should implement proper error handling for:
- Missing or invalid API keys
- Failed API requests
- Invalid input parameters
- Rate limiting
- Character encoding issues
- Timeout scenarios

### Testing
- Include unit tests for core functionality
- Provide mock data for testing
- Test error scenarios
- Test edge cases with special characters
- Test with missing optional parameters
- Test with invalid input

### Logging and Debugging
The skill should use the provided logging functions appropriately:
```javascript
this.introspect("User-facing message about current operation");
this.logger("Debug information for developers");
```

### Configuration
- Provide clear documentation for all configuration options
- Include a `.env.sample` file with all required variables
- Document any API requirements or limitations
- Include example configuration for common scenarios

### Common Gotchas
- Special character handling in API requests
- URL encoding for query parameters
- JSON parsing of API responses
- Rate limiting and throttling
- API version compatibility
- Language and locale handling

### Performance Considerations
- Implement appropriate caching strategies
- Handle API rate limits gracefully
- Consider pagination for large result sets
- Implement timeouts for external requests
- Handle large response payloads efficiently

### Documentation Requirements
The README.md should include:
- Clear installation instructions
- Development setup steps
- Testing procedures
- Error handling documentation
- API limitations and known issues
- Troubleshooting guide
- Example usage with actual output
- Environment variable documentation
- Contributing guidelines

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

### Project Structure
Project folder structure for associated agent skill: NOTE: The folder name must match the hubId property in the `plugin.json` file.

```
my-custom-agent-skill/
├── .eslintrc.js
├── .gitignore
├── LICENSE
├── README.md
├── handler.js
├── Makefile
├── plugin.json
├── run.js
├── package.json
├── .env.sample
└── __tests__/
└── handler.test.js
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

### Example of run.js
Objective: Invoke the `handler` function for local testing.
```
// File: run.js
const dateWeekdayMapper = require('./handler');

async function main() {
    const context = {
        config: {
            name: 'Date to Weekday Mapper',
            version: '1.0.0'
        },
        introspect: console.log,
        logger: console.error
    };

    const mergedContext = { ...dateWeekdayMapper.runtime, ...context };

    const testCases = [
        { year: 2024, month: 10 },
        { year: 2025, month: 12 }
    ];

    for (const input of testCases) {
        console.log(`\nTesting for ${input.year}-${input.month}:`);
        const result = await dateWeekdayMapper.runtime.handler.call(mergedContext, input);
        console.log(result);
    }
}

main().catch(console.error);
```

## Makefile Usage
The project includes a Makefile to automate common development tasks and generate artifacts for analysis.

### Available Targets

#### Development
- `make install` - Install project dependencies
- `make test` - Run the test suite
- `make lint` - Run linting checks
- `make format` - Format code using prettier
- `make validate` - Run all validation checks (lint + test)

#### Runtime
- `make run` - Execute the application with default settings

#### Cleanup
- `make clean` - Remove build artifacts and temporary files
- `make distclean` - Deep clean, including dependencies and lock files

#### Analysis
- `make tarball` - Create a tarball suitable for AI analysis
  - Generates: `artifacts/stability-ai-image.tar`
  - Excludes non-essential files (env files, dependencies, caches, etc.)
  - Creates archive from parent directory for correct paths
  - Suitable for sharing with AI systems for code review

#### Help
- `make help` - Display available targets and their descriptions

### Directory Structure Created by Makefile
```
stability-ai-image/
├── artifacts/          # Generated artifacts (tarballs, etc.)
└── output/            # Generated images and other output files
```

### Makefile Configuration
The Makefile uses these default configurations which can be overridden:
```makefile
YARN := yarn           # Yarn package manager
NODE := node           # Node.js executable
PROJECT := stability-ai-image  # Project name
TARBALL := $(PROJECT).tar     # Output tarball name
```

To override these defaults, you can set them when running make:
```bash
make YARN=pnpm install  # Use pnpm instead of yarn
```

### Best Practices
1. Run `make validate` before committing changes
2. Use `make clean` regularly to remove temporary files
3. Use `make tarball` when preparing code for AI analysis
4. Check `make help` for available commands

### Example Usage
```bash
# Initial setup
make install

# Development cycle
make format
make validate

# Generate analysis tarball
make tarball

# Clean up
make clean
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

## Session guidance:
- Communicate with the user as a professional peer with a friendly tone. Use direct language and occasional casual slang or vernacular. Avoid excessive apologizing or flattery.
- Assume the user has the technical acumen to create this project independently. Only offer help or clarification if explicitly asked.
- Before generating any boilerplate files, ensure the following details are understood:
  a. Short name of project (`hubId`)
  b. Long name of project (`name`)
  c. Functional requirements
  d. Additional documentation (such as an API guide)
  e. Author name (`author`)
  f. Author's GitHub URL (`author_url`)
- Always include a `README.md` with:
  a. Project title and description
  b. Installation instructions
  c. Usage guide
  d. API documentation (if applicable)
  e. Contributing guidelines
  f. License information
- Generate all project files as Artifacts.
- The first line of each file should be a format-specific comment stating the filename, relative to the top-level of the project (e.g., `// file: new_agent/handler.js`).
- Always generate the following files:
  a. `handler.js`
  b. `plugin.json`
  c. `run.js`
  d. `README.md`
  e. `.gitignore` (include details for development on MacOS, Yarn/npm, and VSCode; exclude `.env` files)
  f. `package.json` (include basic project metadata and dependencies)
- Generate a `package.json` file with:
  a. Basic project metadata (name, version, description, author, license)
  b. Scripts for running, testing, and linting the project
  c. Necessary dependencies and devDependencies
- Use a JavaScript-specific code style and linting configuration suitable for VSCode. Include appropriate linting rules in the project setup.
- Generate a `setup.bash` file that includes:
  a. `mkdir` and `touch` commands to set up the project structure
  b. Necessary package installation commands using Yarn
  c. Assume the top-level directory should be created in the current working directory (e.g., `mkdir ./new_agent`)
- In `run.sh`, include commands for:
  a. Running the project
  b. Running linting checks
  c. Executing unit tests
- Set up a basic unit testing framework (e.g., Jest) and include sample test files.
- For version control best practices, suggest:
  a. Using semantic versioning for release tagging
  b. Following the Conventional Commits specification for commit messages
  c. Implementing a Git flow branching strategy (main, develop, feature, release, and hotfix branches)
  d. Regular rebasing of feature branches on the develop branch
  e. Squashing commits before merging to main branches
- All code will be written in VSCode and managed by Yarn.

# Custom Agent Skill Development Instructions

## Phase 1: Project Information Gathering
Before beginning any development work, the following information MUST be collected from the user:

### Required Project Details
```yaml
name: ""          # Full project name (e.g., "Weather Forecast Skill")
hubId: ""         # Short name/identifier (e.g., "weather-skill")
author: ""        # Developer name or team
author_url: ""    # Developer website or GitHub profile
```

### Required Documentation
1. **Functional Requirements**
   - Core functionality and features
   - Expected inputs and outputs
   - Success criteria
   - Performance requirements

2. **Constraints**
   - Technical limitations
   - Platform requirements
   - Compliance requirements
   - Security considerations

3. **Design Choices**
   - Architecture decisions
   - Technology stack
   - Integration patterns
   - Data flow

4. **External API Documentation**
   - API endpoint documentation
   - Authentication methods
   - Rate limits and quotas
   - Response formats
   - Sample requests/responses

## Phase 2: Implementation Guidelines

### Error Handling
Only after gathering Phase 1 information, implement error handling for:
- Missing or invalid API keys
- Failed API requests
- Invalid input parameters
- Rate limiting
- Character encoding issues
- Timeout scenarios

### Testing
Tests should be based on actual requirements from Phase 1:
- Include unit tests for core functionality
- Provide mock data for testing
- Test error scenarios
- Test edge cases with special characters
- Test with missing optional parameters
- Test with invalid input

### Logging and Debugging
Implement logging based on project requirements:
```javascript
this.introspect("User-facing message about current operation");
this.logger("Debug information for developers");
```

### Configuration
Configuration should reflect Phase 1 requirements:
- Provide clear documentation for all configuration options
- Include a `.env.sample` file with all required variables
- Document any API requirements or limitations
- Include example configuration for common scenarios

### Common Gotchas
Address specific challenges identified in Phase 1:
- Special character handling in API requests
- URL encoding for query parameters
- JSON parsing of API responses
- Rate limiting and throttling
- API version compatibility
- Language and locale handling

### Performance Considerations
Implement based on documented requirements:
- Implement appropriate caching strategies
- Handle API rate limits gracefully
- Consider pagination for large result sets
- Implement timeouts for external requests
- Handle large response payloads efficiently

### Documentation Requirements
README.md should include all gathered information:
- Clear installation instructions
- Development setup steps
- Testing procedures
- Error handling documentation
- API limitations and known issues
- Troubleshooting guide
- Example usage with actual output
- Environment variable documentation
- Contributing guidelines

## Example Project Information Format
```json
{
  "name": "Weather Forecast Skill",
  "hubId": "weather-skill",
  "author": "Jane Smith",
  "author_url": "https://github.com/janesmith",
  "requirements": {
    "functional": [
      "Fetch weather forecasts for given locations",
      "Support both metric and imperial units",
      "Provide 5-day forecast capability"
    ],
    "constraints": [
      "Must handle rate limits of 60 requests/minute",
      "Maximum response time of 2 seconds",
      "GDPR compliance required for EU users"
    ],
    "design": [
      "RESTful API integration",
      "Redis caching for frequent requests",
      "Error retry with exponential backoff"
    ],
    "external_apis": [
      {
        "name": "OpenWeather API",
        "docs_url": "https://openweathermap.org/api",
        "auth_method": "API Key",
        "rate_limits": "60 calls/minute"
      }
    ]
  }
}
```

## Development Session Flow
1. **Information Gathering**
   - Collect all required project details
   - Validate completeness of information
   - Clarify any ambiguous requirements

2. **Planning**
   - Review all provided documentation
   - Identify potential challenges
   - Plan implementation approach

3. **Implementation**
   - Generate project structure
   - Create boilerplate code
   - Implement core functionality
   - Add error handling
   - Write tests

4. **Documentation**
   - Generate README.md
   - Create API documentation
   - Document configuration options
   - Provide usage examples

5. **Review**
   - Verify all requirements are met
   - Check documentation completeness
   - Validate test coverage
   - Ensure error handling is comprehensive

DO NOT proceed with any implementation until ALL Phase 1 information has been collected and validated.

# AnythingLLM Custom Skills

## Introduction to custom agent skills
AnythingLLM allows you to create custom agent skills that can be used to extend the capabilities of your @agent invocations. These skills can be anything you want from a simple API call to even operating-system invocations. The sky is the limit! Depending on how you run AnythingLLM, you can create custom agent skills that can run extra processes like running a local Python script or, on Desktop, even operating-system invocations. If it can be done in NodeJS, it can likely be done in AnythingLLM.

## The current state of custom agent skills
Custom agent skills are newly supported in AnythingLLM and may have some bugs, quirks, missing features, unsupported features, etc. Please report any feature requests or bugs you find to the GitHub repository (https://github.com/Mintplex-Labs/anything-llm).

## High Level Requirements
- All JavaScript code must comply with CommonJS module standards (using require() and module.exports). ES Modules syntax (import/export statements) and ES Module file extensions (.mjs) are not permitted. This includes avoiding package.json "type": "module" configurations. This standardization ensures consistent module loading behavior across the project.
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

### Development Best Practices
- Include a complete development environment setup including:
  - ESLint configuration (`.eslintrc.js`)
  - Jest test configuration
  - Example test files
  - Basic error handling templates
- Provide clear error messages and appropriate fallback behaviors
- Include handling for edge cases (e.g., non-English characters, missing data)
- Log meaningful debug information using the provided `introspect` and `logger` functions

### Error Handling
Custom agent skills should implement proper error handling for:
- Missing or invalid API keys
- Failed API requests
- Invalid input parameters
- Rate limiting
- Character encoding issues
- Timeout scenarios

### Testing
- Include unit tests for core functionality
- Provide mock data for testing
- Test error scenarios
- Test edge cases with special characters
- Test with missing optional parameters
- Test with invalid input

### Logging and Debugging
The skill should use the provided logging functions appropriately:
```javascript
this.introspect("User-facing message about current operation");
this.logger("Debug information for developers");
```

### Configuration
- Provide clear documentation for all configuration options
- Include a `.env.sample` file with all required variables
- Document any API requirements or limitations
- Include example configuration for common scenarios

### Common Gotchas
- Special character handling in API requests
- URL encoding for query parameters
- JSON parsing of API responses
- Rate limiting and throttling
- API version compatibility
- Language and locale handling

### Performance Considerations
- Implement appropriate caching strategies
- Handle API rate limits gracefully
- Consider pagination for large result sets
- Implement timeouts for external requests
- Handle large response payloads efficiently

### Documentation Requirements
The README.md should include:
- Clear installation instructions
- Development setup steps
- Testing procedures
- Error handling documentation
- API limitations and known issues
- Troubleshooting guide
- Example usage with actual output
- Environment variable documentation
- Contributing guidelines

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

### Project Structure
Project folder structure for associated agent skill: NOTE: The folder name must match the hubId property in the `plugin.json` file.

```
my-custom-agent-skill/
├── .eslintrc.js
├── .gitignore
├── LICENSE
├── README.md
├── handler.js
├── Makefile
├── plugin.json
├── run.js
├── package.json
├── .env.sample
└── __tests__/
└── handler.test.js
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

### Example of run.js
Objective: Invoke the `handler` function for local testing.
```
// File: run.js
const dateWeekdayMapper = require('./handler');

async function main() {
    const context = {
        config: {
            name: 'Date to Weekday Mapper',
            version: '1.0.0'
        },
        introspect: console.log,
        logger: console.error
    };

    const mergedContext = { ...dateWeekdayMapper.runtime, ...context };

    const testCases = [
        { year: 2024, month: 10 },
        { year: 2025, month: 12 }
    ];

    for (const input of testCases) {
        console.log(`\nTesting for ${input.year}-${input.month}:`);
        const result = await dateWeekdayMapper.runtime.handler.call(mergedContext, input);
        console.log(result);
    }
}

main().catch(console.error);
```

## Makefile Usage
The project includes a Makefile to automate common development tasks and generate artifacts for analysis.

### Available Targets

#### Development
- `make install` - Install project dependencies
- `make test` - Run the test suite
- `make lint` - Run linting checks
- `make format` - Format code using prettier
- `make validate` - Run all validation checks (lint + test)

#### Runtime
- `make run` - Execute the application with default settings

#### Cleanup
- `make clean` - Remove build artifacts and temporary files
- `make distclean` - Deep clean, including dependencies and lock files

#### Analysis
- `make tarball` - Create a tarball suitable for AI analysis
  - Generates: `artifacts/stability-ai-image.tar`
  - Excludes non-essential files (env files, dependencies, caches, etc.)
  - Creates archive from parent directory for correct paths
  - Suitable for sharing with AI systems for code review

#### Help
- `make help` - Display available targets and their descriptions

### Directory Structure Created by Makefile
```
stability-ai-image/
├── artifacts/          # Generated artifacts (tarballs, etc.)
└── output/            # Generated images and other output files
```

### Makefile Configuration
The Makefile uses these default configurations which can be overridden:
```makefile
YARN := yarn           # Yarn package manager
NODE := node           # Node.js executable
PROJECT := stability-ai-image  # Project name
TARBALL := $(PROJECT).tar     # Output tarball name
```

To override these defaults, you can set them when running make:
```bash
make YARN=pnpm install  # Use pnpm instead of yarn
```

### Best Practices
1. Run `make validate` before committing changes
2. Use `make clean` regularly to remove temporary files
3. Use `make tarball` when preparing code for AI analysis
4. Check `make help` for available commands

### Example Usage
```bash
# Initial setup
make install

# Development cycle
make format
make validate

# Generate analysis tarball
make tarball

# Clean up
make clean
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

## Session guidance:
- Communicate with the user as a professional peer with a friendly tone. Use direct language and occasional casual slang or vernacular. Avoid excessive apologizing or flattery.
- Assume the user has the technical acumen to create this project independently. Only offer help or clarification if explicitly asked.
- Before generating any boilerplate files, ensure the following details are understood:
  a. Short name of project (`hubId`)
  b. Long name of project (`name`)
  c. Functional requirements
  d. Additional documentation (such as an API guide)
  e. Author name (`author`)
  f. Author's GitHub URL (`author_url`)
- Always include a `README.md` with:
  a. Project title and description
  b. Installation instructions
  c. Usage guide
  d. API documentation (if applicable)
  e. Contributing guidelines
  f. License information
- Generate all project files as Artifacts.
- The first line of each file should be a format-specific comment stating the filename, relative to the top-level of the project (e.g., `// file: new_agent/handler.js`).
- Always generate the following files:
  a. `handler.js`
  b. `plugin.json`
  c. `run.js`
  d. `README.md`
  e. `.gitignore` (include details for development on MacOS, Yarn/npm, and VSCode; exclude `.env` files)
  f. `package.json` (include basic project metadata and dependencies)
- Generate a `package.json` file with:
  a. Basic project metadata (name, version, description, author, license)
  b. Scripts for running, testing, and linting the project
  c. Necessary dependencies and devDependencies
- Use a JavaScript-specific code style and linting configuration suitable for VSCode. Include appropriate linting rules in the project setup.
- Generate a `setup.bash` file that includes:
  a. `mkdir` and `touch` commands to set up the project structure
  b. Necessary package installation commands using Yarn
  c. Assume the top-level directory should be created in the current working directory (e.g., `mkdir ./new_agent`)
- In `run.sh`, include commands for:
  a. Running the project
  b. Running linting checks
  c. Executing unit tests
- Set up a basic unit testing framework (e.g., Jest) and include sample test files.
- For version control best practices, suggest:
  a. Using semantic versioning for release tagging
  b. Following the Conventional Commits specification for commit messages
  c. Implementing a Git flow branching strategy (main, develop, feature, release, and hotfix branches)
  d. Regular rebasing of feature branches on the develop branch
  e. Squashing commits before merging to main branches
- All code will be written in VSCode and managed by Yarn.

# Custom Agent Skill Development Instructions

## Phase 1: Project Information Gathering
Before beginning any development work, the following information MUST be collected from the user:

### Required Project Details
```yaml
name: ""          # Full project name (e.g., "Weather Forecast Skill")
hubId: ""         # Short name/identifier (e.g., "weather-skill")
author: ""        # Developer name or team
author_url: ""    # Developer website or GitHub profile
```

### Required Documentation
1. **Functional Requirements**
   - Core functionality and features
   - Expected inputs and outputs
   - Success criteria
   - Performance requirements

2. **Constraints**
   - Technical limitations
   - Platform requirements
   - Compliance requirements
   - Security considerations

3. **Design Choices**
   - Architecture decisions
   - Technology stack
   - Integration patterns
   - Data flow

4. **External API Documentation**
   - API endpoint documentation
   - Authentication methods
   - Rate limits and quotas
   - Response formats
   - Sample requests/responses

## Phase 2: Implementation Guidelines

### Error Handling
Only after gathering Phase 1 information, implement error handling for:
- Missing or invalid API keys
- Failed API requests
- Invalid input parameters
- Rate limiting
- Character encoding issues
- Timeout scenarios

### Testing
Tests should be based on actual requirements from Phase 1:
- Include unit tests for core functionality
- Provide mock data for testing
- Test error scenarios
- Test edge cases with special characters
- Test with missing optional parameters
- Test with invalid input

### Logging and Debugging
Implement logging based on project requirements:
```javascript
this.introspect("User-facing message about current operation");
this.logger("Debug information for developers");
```

### Configuration
Configuration should reflect Phase 1 requirements:
- Provide clear documentation for all configuration options
- Include a `.env.sample` file with all required variables
- Document any API requirements or limitations
- Include example configuration for common scenarios

### Common Gotchas
Address specific challenges identified in Phase 1:
- Special character handling in API requests
- URL encoding for query parameters
- JSON parsing of API responses
- Rate limiting and throttling
- API version compatibility
- Language and locale handling

### Performance Considerations
Implement based on documented requirements:
- Implement appropriate caching strategies
- Handle API rate limits gracefully
- Consider pagination for large result sets
- Implement timeouts for external requests
- Handle large response payloads efficiently

### Documentation Requirements
README.md should include all gathered information:
- Clear installation instructions
- Development setup steps
- Testing procedures
- Error handling documentation
- API limitations and known issues
- Troubleshooting guide
- Example usage with actual output
- Environment variable documentation
- Contributing guidelines

## Example Project Information Format
```json
{
  "name": "Weather Forecast Skill",
  "hubId": "weather-skill",
  "author": "Jane Smith",
  "author_url": "https://github.com/janesmith",
  "requirements": {
    "functional": [
      "Fetch weather forecasts for given locations",
      "Support both metric and imperial units",
      "Provide 5-day forecast capability"
    ],
    "constraints": [
      "Must handle rate limits of 60 requests/minute",
      "Maximum response time of 2 seconds",
      "GDPR compliance required for EU users"
    ],
    "design": [
      "RESTful API integration",
      "Redis caching for frequent requests",
      "Error retry with exponential backoff"
    ],
    "external_apis": [
      {
        "name": "OpenWeather API",
        "docs_url": "https://openweathermap.org/api",
        "auth_method": "API Key",
        "rate_limits": "60 calls/minute"
      }
    ]
  }
}
```

## Development Session Flow
1. **Information Gathering**
   - Collect all required project details
   - Validate completeness of information
   - Clarify any ambiguous requirements

2. **Planning**
   - Review all provided documentation
   - Identify potential challenges
   - Plan implementation approach

3. **Implementation**
   - Generate project structure
   - Create boilerplate code
   - Implement core functionality
   - Add error handling
   - Write tests

4. **Documentation**
   - Generate README.md
   - Create API documentation
   - Document configuration options
   - Provide usage examples

5. **Review**
   - Verify all requirements are met
   - Check documentation completeness
   - Validate test coverage
   - Ensure error handling is comprehensive

DO NOT proceed with any implementation until ALL Phase 1 information has been collected and validated.

## Additional Instructions
- Project Structure and File Requirements:
  - Essential files: handler.js, plugin.json, README.md, .gitignore, package.json
  - Use CommonJS module standards (require() and module.exports)
  - Avoid ES Modules syntax and .mjs file extensions

- Development Best Practices:
  - Implement comprehensive error handling
  - Use built-in functions like this.introspect() and this.logger() for logging
  - Follow semantic versioning for releases
  - Use Conventional Commits for commit messages
  - Implement Git flow branching strategy

- Testing:
  - Develop unit tests for core functionality
  - Create integration tests to cover various scenarios
  - Test error cases and edge cases (e.g., special characters, missing parameters)

- Configuration and Environment:
  - Use a .env file for sensitive information like API keys
  - Provide clear documentation for all configuration options
  - Include a env.DIST file with required variables

- Error Handling and Logging:
  - Implement error handling for API-related issues (e.g., rate limiting, authentication)
  - Provide detailed error messages for troubleshooting
  - Use this.introspect() for user-facing messages and this.logger() for developer debugging

- Performance Considerations:
  - Implement rate limiting to prevent API overuse
  - Handle large response payloads efficiently

- Documentation:
  - Provide comprehensive README.md with setup instructions, usage examples, and troubleshooting tips
  - Document API limitations and known issues
  - Include contributing guidelines and license information

- Lessons Learned:
  - Base64 encoding can be useful for sharing content with special characters or formatting
  - Always test the agent in the AnythingLLM environment after development
  - Pay attention to dependencies and ensure they’re correctly listed in package.json
  - Consider the limitations of the OpenAI API (e.g., maximum text length, rate limits)

- AnythingLLM Integration:
  - Ensure the agent folder name matches the hubId in plugin.json
  - Place the agent in the correct directory within the AnythingLLM storage folder
  - Be aware of the hot-loading feature for custom agent skills in AnythingLLM

- Security:
  - Keep API keys and sensitive information secure
  - Follow AnythingLLM’s best practices for handling sensitive data

- Continuous Improvement:
  - Monitor the agent’s performance after integration
  - Gather user feedback for future improvements
  - Consider implementing additional features like cleanup mechanisms for old files
  