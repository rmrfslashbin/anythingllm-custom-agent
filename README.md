# AnythingLLM Custom Skills

## GitHub Repository

[https://github.com/rmrfslashbin/anythingllm-custom-agent](https://github.com/rmrfslashbin/anythingllm-custom-agent)

## Introduction

AnythingLLM allows you to create custom agent skills to extend the capabilities of your @agent invocations. These skills can range from simple API calls to operating-system invocations, offering a wide range of possibilities for customization.

## Current State

Custom agent skills are a new feature in AnythingLLM and may have some bugs, quirks, or missing features. Please report any issues or feature requests to the [GitHub repository](https://github.com/rmrfslashbin/anythingllm-custom-agent).

## Requirements

- NodeJS programming experience
- Skills must match the requirements listed in the documentation
- All skills must return a string type response

## Availability

- Docker image: Since commit d1103e or release v1.2.2
- AnythingLLM Desktop: Version 1.6.5 and later
- Not available in AnythingLLM Cloud offering

## Development Prerequisites

- NodeJS 18+
- Yarn
- AnythingLLM running in a supported environment

## Guidelines for Creating Custom Agent Skills

1. Write skills in JavaScript for NodeJS environment
2. Bundle any required NodeJS packages within your skill folder
3. All functions must return a string value
4. Provide a `README.md` file with description and usage instructions
5. Define your plugin with a `plugin.json` file
6. Define the entry point as `handler.js`
7. Wrap your skill in a folder with the same name as the `hubId` in `plugin.json`

## Hot Loading

AnythingLLM supports hot loading of custom agent skills. Changes take effect after exiting the current session or reloading the page for new skills.

## Skill Placement

Place skills in the `plugins/agent-skills` subfolder within your AnythingLLM storage directory.

## Where is my data located?

All data pertaining to AnythingLLM Desktop will be in the following locations. Please replace `<usr>` with your device username.

- On Mac: `/Users/<usr>/Library/Application Support/anythingllm-desktop/storage`
- On Linux: `/Users/<usr>/.config/anythingllm-desktop/storage/`
- On Windows: `C:\User\<usr>\AppData\Roaming\anythingllm-desktop\storage`

## File Structure

```
plugins/agent-skills/my-custom-agent-skill
|-- plugin.json
|-- handler.js
|-- README.md
|-- (additional files as needed)
```

## Key Components

1. `plugin.json`: Defines the skill's properties, setup arguments, examples, and entry point
2. `handler.js`: Contains the main logic of the skill
3. `README.md`: Provides documentation for the skill

## Managing Submodules

To manage custom agent skills as submodules in your Git repository, follow these steps:

1. Navigate to your primary repository's root directory:
   ```bash
   cd /path/to/anythingllm-custom-agent
   ```

2. Add the submodule using the `git submodule add` command:
   ```bash
   git submodule add git@github.com:rmrfslashbin/anythingllm-custom-agent-timestamp.git
   ```

3. Commit the changes:
   ```bash
   git commit -m "Add anythingllm-custom-agent-timestamp submodule"
   ```

4. Push the changes to the remote repository:
   ```bash
   git push origin main
   ```

### Important Notes on Submodules:

- The submodule will be cloned into a directory with the same name as the repository. To use a different name, specify it at the end of the `git submodule add` command.

- When cloning the primary repository in the future, initialize and update the submodules:
  ```bash
  git clone https://github.com/rmrfslashbin/anythingllm-custom-agent.git
  cd anythingllm-custom-agent
  git submodule init
  git submodule update
  ```

- To update the submodule to its latest commit:
  ```bash
  git submodule update --remote
  ```

## Development Resources

For detailed information on creating custom agent skills, including `plugin.json` structure, `handler.js` implementation, and best practices, refer to the full documentation.

## Contributing

We welcome contributions to improve and expand the custom skills feature. Please submit pull requests or issues to the [AnythingLLM Custom Agent GitHub repository](https://github.com/rmrfslashbin/anythingllm-custom-agent).

## License
MIT License
