# Updated Guide to Creating Custom Agent Skills for AnythingLLM

## Introduction
AnythingLLM allows you to create custom agent skills to extend the capabilities of your @agent invocations. These skills can range from simple API calls to operating system invocations.

## Requirements
- NodeJS 18+
- Yarn
- AnythingLLM running in a supported environment

## Availability
- Docker image: Since commit d1103e or release v1.2.2
- Desktop: Version 1.6.5 and later
- Not available in AnythingLLM Cloud offering

## File Structure
```
plugins/agent-skills/your-skill-name
|-- plugin.json
|-- handler.js
|-- README.md
|-- run.js (for testing)
|-- package.json
|-- (other files as needed)
```

## Key Components

### 1. plugin.json
This file defines the skill's metadata and configuration.

Key fields:
- `active`: Boolean to determine if the skill is active
- `hubId`: Unique identifier (must match folder name)
- `name`: Human-readable name of the skill
- `entrypoint`: Defines the main file and expected parameters
- `setup_args`: Configuration arguments for the skill
- `examples`: Sample prompts and calls to guide the LLM

### 2. handler.js
This file contains the main logic of your custom skill.

Key points:
- Must export a `runtime` object with a `handler` function
- The `handler` function should return a string
- Use `try/catch` for error handling
- Access runtime arguments via `this.runtimeArgs`
- Log thoughts with `this.introspect()`
- Log debug messages with `this.logger()`

### 3. README.md
Provide a description, requirements, and usage instructions for your skill.

### 4. run.js
Create test cases to verify your skill's functionality.

### 5. package.json
Define your project's metadata and dependencies.

## Best Practices and Lessons Learned
1. Keep skills focused on a single task or capability
2. Provide clear and diverse examples in the `plugin.json` file
3. Use proper error handling in the `handler.js`
4. Avoid requiring external modules in the global scope
5. Use the provided logging methods for debugging and user feedback
6. Thoroughly test your skill with various inputs
7. Consider security implications, especially for skills that interact with the host system
8. Use clear and descriptive names for your skill and its functions
9. Keep your README up-to-date with accurate usage instructions and examples
10. Version your skill appropriately, using semantic versioning

## Gaps and Boilerplate Items
When creating a new custom agent skill, users often request boilerplate items. To address this, we recommend creating a script or template that generates the basic structure of a custom agent skill, including:

- A basic `plugin.json` file with placeholders for key information
- A `handler.js` file with a basic structure and error handling
- A `README.md` template
- A `run.js` file with a basic test structure
- A `package.json` file with common fields

## AI/User Interaction Guidelines

When assisting users in creating a custom agent skill, follow these steps:

1. Initial Questions:
   a) Ask for the project name (both a long "name" and a short "hubId")
   b) Request `author` and `author_url` data
   c) Ask for the initial version number
   d) Inquire about functional requirements, use cases, goals/objectives, and any additional external resources

2. Summarization:
   - Summarize the functional requirements, objectives, examples, and other aspects of the project
   - Ask the user if they're ready to build the boilerplate project or if they want to make any changes

3. Boilerplate Generation:
   - Once the user confirms, generate the boilerplate project including:
     - Content for all required files (`plugin.json`, `handler.js`, `README.md`, `run.js`, `package.json`)
     - BASH scripts for project setup (if necessary)
     - Any other required components based on the skill's specific needs

4. Review and Iteration:
   - Present the generated boilerplate to the user
   - Offer to make any necessary adjustments or additions

5. Next Steps:
   - Guide the user on how to test and further develop their custom agent skill
   - Provide information on how to integrate the skill into their AnythingLLM environment

Remember to be responsive to user feedback and questions throughout this process, and be prepared to explain or modify any part of the generated boilerplate as needed.

## Conclusion
Creating custom agent skills for AnythingLLM allows you to extend its capabilities in powerful ways. By following this guide and best practices, you can create effective, secure, and user-friendly skills that enhance the AnythingLLM experience.