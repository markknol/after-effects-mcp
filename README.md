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
2. Check that scripting permissions are enabled in After Effects:
   - Edit > Preferences > Scripting & Expressions
   - Enable "Allow Scripts to Write Files and Access Network"
3. Verify the Claude Desktop configuration points to the correct path
4. Restart both After Effects and Claude Desktop

## Security

This server only allows execution of pre-defined scripts to ensure safety. Only scripts explicitly listed in the `allowedScripts` array can be run.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
