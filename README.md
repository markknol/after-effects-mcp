# After Effects MCP Server

This Model Context Protocol (MCP) server connects AI assistants like Claude to Adobe After Effects, allowing them to interact with After Effects projects and run pre-defined scripts.

## Features

- View composition details from After Effects projects
- Run pre-defined read-only automation scripts
- Get detailed information about layers, compositions, and project settings
- Connect Claude AI directly to After Effects

## Installation

1. Clone this repository:
```bash
git clone https://github.com/yourusername/after-effects-mcp.git
cd after-effects-mcp
```

2. Install dependencies:
```bash
npm install
```

3. Install the MCP Bridge panel in After Effects (requires administrator privileges):
```bash
npm run install-bridge
```

4. Connect to Claude Desktop by updating `%APPDATA%\Claude\claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "after-effects": {
      "command": "node",
      "args": [
        "C:\\path\\to\\after-effects-mcp\\build\\index.js"
      ]
    }
  }
}
```

## Usage

1. Open After Effects
2. Open the MCP Bridge panel: Window > mcp-bridge-auto.jsx
3. Launch Claude Desktop
4. Ask Claude to perform tasks in After Effects, such as:
   - "Show me the compositions in my After Effects project"
   - "Get information about the active composition"
   - "What layers are in my current composition?"

## Available Scripts

- `getProjectInfo` - Returns project metadata
- `listCompositions` - Lists all compositions and their properties
- `getLayerInfo` - Returns detailed information about layers in the active composition

## Resources
- `aftereffects://compositions` - Lists all compositions in the current project

## Tools
- `run-script` - Executes predefined After Effects scripts
- `get-results` - Retrieves results from the last script execution

## Troubleshooting

If Claude is unable to communicate with After Effects:
1. Ensure the MCP Bridge panel is open in After Effects
   
   ![MCP Bridge Auto panel in After Effects](./assets/MCP%20Auto%20Bridge.png)
   
   *The MCP Bridge Auto panel should be open and running in After Effects*
   
2. Check that scripting permissions are enabled in After Effects:
   - Edit > Preferences > Scripting & Expressions
   - Enable "Allow Scripts to Write Files and Access Network"
3. Verify the Claude Desktop configuration points to the correct path
4. Restart both After Effects and Claude Desktop

## Security

This server only allows execution of pre-defined scripts to ensure safety. Only scripts explicitly listed in the `allowedScripts` array can be run.

## Contributing

Contributions to improve the After Effects MCP Server are welcome! Here's how you can contribute:

### Development Setup

1. **Fork and clone the repository**
   ```bash
   git clone https://github.com/yourusername/after-effects-mcp.git
   cd after-effects-mcp
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Build the project**
   ```bash
   npm run build
   ```

4. **Install the MCP Bridge in After Effects**
   ```bash
   npm run install-bridge
   ```

### Development Workflow

1. **Make your changes** to the source code in the `src` directory
2. **Build the project** to transpile TypeScript to JavaScript
   ```bash
   npm run build
   ```
3. **Test your changes** by running the server and connecting via Claude Desktop
   ```bash
   npm run start
   ```

### Adding New Scripts

1. Create new ExtendScript files in the `src/scripts` directory
2. Update the `allowedScripts` array in `src/index.ts` to include your new scripts
3. Build the project to copy the scripts to the build folder

### Pull Request Process

1. Ensure your code follows the existing style
2. Update the README.md with details of changes if appropriate
3. The PR should work with the latest stable version of Adobe After Effects
4. Include a description of what your changes do and why they should be included

### Code of Conduct

Please be respectful and considerate of others when contributing to this project.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
