# After Effects MCP Server 🎬

This Model Context Protocol (MCP) server connects AI assistants (like Claude, or any MCP-compatible client) to Adobe After Effects. It enables remote control and automation of After Effects projects using ExtendScript, bridging the gap between natural language commands and complex AE tasks.

## Features

*   **Composition Management:**
    *   List all compositions and their properties (`listCompositions`).
    *   Create new compositions with specific settings (`createComposition` / `mcp_aftereffects_create_composition` tool).
    *   Get general project information (`getProjectInfo`).
*   **Layer Creation:**
    *   Create Text Layers with custom content, font, size, color, alignment, position, and timing (`createTextLayer`).
    *   Create Shape Layers (Rectangle, Ellipse, Polygon/Star) with control over size, position, fill, stroke, and timing (`createShapeLayer`).
    *   Create Solid Layers (including Adjustment Layers) with custom color, size, position, and timing (`createSolidLayer`).
*   **Layer Manipulation:**
    *   Modify properties of existing layers (Position, Scale, Rotation, Opacity, Start Time, Duration) using `setLayerProperties`.
    *   Update text content and style (Font Family, Font Size, Fill Color) for existing Text Layers using `setLayerProperties`.
    *   Get basic information about layers in the active composition (`getLayerInfo`).
*   **Remote Execution:** Run predefined, allowed ExtendScript functions within After Effects via the `mcp_aftereffects_run_script` tool.
*   **MCP Integration:** Exposes functionality through standard MCP tools and resources.

## Installation

1.  **Prerequisites:**
    *   Node.js (LTS version recommended)
    *   Adobe After Effects (tested with version 2024, may work with others)
2.  **Clone Repository:**
    ```bash
    # Replace with your actual repository URL if different
    git clone https://github.com/daxgalt/after-effects-mcp.git
    cd after-effects-mcp
    ```
3.  **Install Dependencies:**
    ```bash
    npm install
    ```
4.  **Build the Server:**
    ```bash
    npm run build
    ```
5.  **Install AE Bridge Script:** (requires administrator privileges)
    ```bash
    node install-bridge.js
    # On macOS, you might need: sudo node install-bridge.js
    ```
    This copies the necessary `mcp-bridge-auto.jsx` to the After Effects `Scripts/ScriptUI Panels` folder.
6.  **Configure After Effects:**
    *   Launch After Effects.
    *   Enable scripting permissions: `Edit > Preferences > Scripting & Expressions` (or `After Effects > Settings > Scripting & Expressions` on macOS).
    *   Check **"Allow Scripts to Write Files and Access Network"**.
7.  **(Optional) Configure MCP Client:**
    *   Update your MCP client (e.g., Claude Desktop `claude_desktop_config.json`) to point to the server. Replace `C:\\\\path\\\\to\\\\after-effects-mcp` with the actual path to this repository folder on your system.
    ```json
    {
      "mcpServers": {
        "after-effects": {
          "command": "node",
          "args": [
            "C:\\\\path\\\\to\\\\after-effects-mcp\\\\build\\\\index.js"
          ]
        }
      }
    }
    ```

## Usage

1.  **Start After Effects** and open a project (or create a new one).
2.  **Open the MCP Bridge Panel:** In After Effects, go to `Window > mcp-bridge-auto.jsx`. Ensure the panel is open and the "Auto-run commands" checkbox is checked.
3.  **Start the MCP Server:** From your terminal in the repository directory:
    ```bash
    npm run start
    # Or: node build/index.js
    ```
4.  **Connect your MCP Client** (e.g., launch Claude Desktop if configured, or use another MCP tool).
5.  **Interact:** Ask your AI assistant to perform tasks using the available tools and scripts, or call the tools directly.

### Example MCP Tool Calls

*(These examples assume you are using an MCP client capable of calling tools with parameters, like Cursor)*

*   **Create a Composition:**
    ```javascript
    // Using the dedicated tool
    mcp_aftereffects_create_composition({
      name: "My Awesome Comp",
      width: 1920, height: 1080,
      duration: 15, frameRate: 24,
      backgroundColor: { r: 50, g: 50, b: 50 }
    });

    // Using run-script (alternative)
    mcp_aftereffects_run_script({
      script: "createComposition",
      parameters: { /* same parameters as above */ }
    });
    ```
*   **Create a Text Layer:**
    ```javascript
    mcp_aftereffects_run_script({
      script: "createTextLayer",
      parameters: {
        compName: "My Awesome Comp",
        text: "Hello MCP!",
        fontFamily: "Arial", fontSize: 100,
        position: [960, 540],
        color: [1, 1, 0] // Yellow
      }
    });
    ```
*   **Create a Shape Layer:**
    ```javascript
    mcp_aftereffects_run_script({
      script: "createShapeLayer",
      parameters: {
        compName: "My Awesome Comp",
        name: "Red Circle",
        shapeType: "ellipse", size: [300, 300],
        fillColor: [1, 0, 0],
        position: [400, 540],
        strokeWidth: 0
      }
    });
    ```
*   **Create an Adjustment Layer:**
    ```javascript
    mcp_aftereffects_run_script({
      script: "createSolidLayer",
      parameters: {
        compName: "My Awesome Comp",
        name: "Effects Adjuster",
        isAdjustment: true,
        duration: 15
      }
    });
    ```
*   **Modify Layer Properties (e.g., change text font and scale):**
    ```javascript
    mcp_aftereffects_run_script({
      script: "setLayerProperties",
      parameters: {
        compName: "My Awesome Comp",
        layerName: "Hello MCP!", // Target the text layer by name
        fontFamily: "Impact",
        fontSize: 120,
        scale: [110, 110] // Scale up slightly
      }
    });
    ```
*   **List Compositions:**
    ```javascript
    mcp_aftereffects_run_script({ script: "listCompositions" });
    ```
*   **Get Results:** (After running an asynchronous command like `createComposition` or `run-script`)
    ```javascript
    mcp_aftereffects_get_results({});
    ```

## Available Tools & Scripts

This server exposes functionality through dedicated MCP tools and allowed scripts run via the `mcp_aftereffects_run_script` tool.

### Dedicated MCP Tools

*   `mcp_aftereffects_create_composition`: Creates a new composition (provides typed parameters).
*   `mcp_aftereffects_get_results`: Retrieves results from the last asynchronous script execution.
*   `mcp_aftereffects_get_help`: Provides basic help text.

### Scripts Callable via `mcp_aftereffects_run_script`

*(Use the `script` parameter for the name and `parameters` for the arguments object)*

*   `getProjectInfo`: Returns project metadata.
*   `listCompositions`: Lists all compositions and their properties.
*   `getLayerInfo`: Returns basic info about layers in the *active* composition.
*   `createComposition`: Creates a new composition (alternative to dedicated tool).
*   `createTextLayer`: Creates text layers with styling and timing.
*   `createShapeLayer`: Creates shape layers (rectangle, ellipse, polygon, star) with styling and timing.
*   `createSolidLayer`: Creates solid or adjustment layers with timing.
*   `setLayerProperties`: Modifies properties (transform, opacity, timing, text content/style) of existing layers.

## Troubleshooting

*   **"Unknown Command" Errors:** Ensure the `mcp-bridge-auto.jsx` panel is running in AE, the server was rebuilt (`npm run build`), and scripts reinstalled (`node install-bridge.js`) after code changes. Check the `allowedScripts` array in `src/index.ts`.
*   **"Cannot Access Property/Method" / TypeErrors:** Often indicates an issue within the ExtendScript code (`mcp-bridge-auto.jsx`). Check AE console (`Window > Console`) or ExtendScript Toolkit for errors. The logs in the MCP Bridge panel might also show the error.
*   **Composition Not Found:** Make sure the target composition name is spelled correctly and the composition is open in AE. Having the composition panel active can sometimes help.
*   **File Access Errors:** Verify AE has permissions (`Preferences > Scripting & Expressions > Allow Scripts to Write Files...`) and check permissions for the system temporary directory where `ae_command.json` and `ae_mcp_result.json` are written.
*   **Font Not Applied:** Ensure the specified `fontFamily` name matches exactly how it appears in After Effects. Not all fonts may be accessible to ExtendScript depending on installation and type. Try standard system fonts first.
*   **Check Panel Logs:** The "Command Log" within the `mcp-bridge-auto.jsx` panel in AE provides valuable debugging information, especially with the detailed logging we added.

   ![MCP Bridge Auto panel in After Effects](./assets/MCP%20Auto%20Bridge.png)

   *The MCP Bridge Auto panel showing logs*

## Security

This server uses an allow-list (`allowedScripts` in `src/index.ts`) to restrict executable scripts. Only scripts present in this list and the defined MCP tools can be triggered remotely. Use caution if adding scripts that modify files or execute system commands.

## Contributing

Contributions are welcome! Please follow standard fork/pull request procedures.

### Development Setup

(Steps are the same as Installation steps 2-5)

### Development Workflow

1.  Make changes in the `src` directory (TypeScript server code in `index.ts` or ExtendScript functions in `mcp-bridge-auto.jsx`).
2.  Rebuild: `npm run build`
3.  Re-install AE script if `mcp-bridge-auto.jsx` was changed: `node install-bridge.js`
4.  Restart the server: `npm run start`
5.  Restart AE and reopen the bridge panel if needed.
6.  Test changes thoroughly.

### Adding New Functionality (Scripts)

1.  Add your new ExtendScript function directly into `src/scripts/mcp-bridge-auto.jsx` (following the consolidated pattern).
2.  Add a new `case` to the `switch` statement in the `executeCommand` function within `mcp-bridge-auto.jsx` to call your new function.
3.  Add the **string name** you used in the `case` statement to the `allowedScripts` array in `src/index.ts`.
4.  Document the new script and its parameters in this README.
5.  Rebuild, re-install, restart, and test.

## License

This project is licensed under the MIT License - see the `LICENSE` file for details.
