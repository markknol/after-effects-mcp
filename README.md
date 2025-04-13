# After Effects MCP Server 🎬

**Connect AI assistants to Adobe After Effects!**

This project acts as a bridge (using the Model Context Protocol - MCP) allowing AI tools (like Claude, Cursor, etc.) to remotely control Adobe After Effects. You can ask the AI to perform common tasks like creating compositions, adding text, shapes, or solids, and modifying layer properties, all through natural language or simple commands.

It works by running a small server locally that listens for commands from the AI and translates them into actions within After Effects using its native scripting language (ExtendScript).

---

**Table of Contents**

*   [✨ Features](#-features)
*   [🚀 Quick Start](#-quick-start)
*   [🛠️ Installation & Setup](#️-installation--setup)
*   [💻 Usage](#-usage)
    *   [Running the Server & Bridge](#running-the-server--bridge)
    *   [Example Commands](#example-commands)
*   [⚙️ Available Commands (Detailed)](#️-available-commands-detailed)
    *   [Dedicated MCP Tools](#dedicated-mcp-tools)
    *   [Scripts via `run-script`](#scripts-via-run-script)
*   [❓ Troubleshooting](#-troubleshooting)
*   [🔒 Security](#-security)
*   [🤝 Contributing](#-contributing)
    *   [Development Setup](#development-setup)
    *   [Adding New Features](#adding-new-features)
*   [📜 License](#-license)

---

## ✨ Features

*   **View Project Info:** Get details about compositions and the overall project.
*   **Create Compositions:** Make new compositions with custom sizes, durations, backgrounds, etc.
*   **Create Layers:**
    *   **Text:** Add text with specific fonts, sizes, colors, and placement.
    *   **Shapes:** Add rectangles, ellipses, stars, or polygons with colors and strokes.
    *   **Solids & Adjustment Layers:** Add solid color backgrounds or adjustment layers for effects.
*   **Modify Layers:** Change properties like position, scale, rotation, opacity, timing, and text content/style after creation.
*   **MCP Compatible:** Works with AI assistants and tools that support the Model Context Protocol.

---

## 🚀 Quick Start

1.  **Install:** Clone the repo, run `npm install`, then `npm run build`, then `node install-bridge.js` (with `sudo` on macOS if needed).
2.  **Configure AE:** Allow scripts to Write Files and Access Network in AE Preferences (`Edit > Preferences > Scripting & Expressions`).
3.  **Run AE Bridge:** Open After Effects, then open the panel via `Window > mcp-bridge-auto.jsx`. Keep it open.
4.  **Run Server:** In your terminal (in the project folder), run `npm run start`.
5.  **Connect AI:** Configure your MCP client (if needed) and start interacting!

*(See [Installation & Setup](#️-installation--setup) for more details)*

---

## 🛠️ Installation & Setup

Follow these steps to get the server running:

1.  **Prerequisites:**
    *   Node.js (LTS version recommended - download from [nodejs.org](https://nodejs.org/))
    *   Adobe After Effects (e.g., version 2024)
2.  **Clone Repository:**
    Get the code onto your computer.
    ```bash
    # Replace with the correct URL if you forked it
    git clone https://github.com/daxgalt/after-effects-mcp.git
    cd after-effects-mcp
    ```
3.  **Install Dependencies:**
    This downloads the necessary helper libraries for the server.
    ```bash
    npm install
    ```
4.  **Build the Server:**
    This prepares the server code to be run.
    ```bash
    npm run build
    ```
5.  **Install After Effects Bridge Script:**
    This copies the communication panel script into After Effects. You'll likely need administrator rights.
    ```bash
    # Windows (run in an Admin terminal)
    node install-bridge.js

    # macOS
    sudo node install-bridge.js
    ```
    *Troubleshooting:* If this fails, manually copy `build/scripts/mcp-bridge-auto.jsx` to your After Effects `Scripts/ScriptUI Panels` folder. ([Find Script Folders](https://helpx.adobe.com/after-effects/using/scripts.html#InstallaScript))
6.  **Configure After Effects Permissions:**
    *   Start After Effects.
    *   Go to `Edit > Preferences > Scripting & Expressions` (Windows) or `After Effects > Settings > Scripting & Expressions` (macOS).
    *   **Check the box:** `Allow Scripts to Write Files and Access Network`. This is required for the bridge panel to work. Click OK.
7.  **(Optional) Configure Your AI/MCP Client:**
    *   If your AI tool needs explicit configuration (like Claude Desktop), you'll need to tell it how to run this server. Edit its configuration file (e.g., `%APPDATA%\Claude\claude_desktop_config.json` on Windows) to add an entry like this, **making sure to use the correct full path** to where you cloned *this* repository:
    ```json
    {
      "mcpServers": {
        "after-effects": {
          "command": "node",
          "args": [
            "C:\\Users\\YourUser\\path\\to\\after-effects-mcp\\build\\index.js" // <-- CHANGE THIS PATH!
          ]
        }
      }
    }
    ```
    *   Other tools might automatically detect MCP servers or have different configuration methods.

---

## 💻 Usage

### Running the Server & Bridge

For the AI to control After Effects, both AE and the server must be running:

1.  **Start After Effects:** Open your AE project.
2.  **Open the Bridge Panel:** In After Effects, go to `Window > mcp-bridge-auto.jsx`. A small panel should appear.
    *   **Crucially:** Keep this panel open while you want the AI connection to work.
    *   Make sure the "Auto-run commands" checkbox inside the panel is checked.
3.  **Start the MCP Server:** Open your terminal/command prompt, navigate to the `after-effects-mcp` folder you cloned, and run:
    ```bash
    npm run start
    ```
    Leave this terminal window running.
4.  **Use your AI:** Now, connect and interact with your MCP-compatible AI tool.

### Example Commands

Here are examples of how an AI (like Cursor) might call the available tools:

*   **Create a Composition:**
    ```javascript
    mcp_aftereffects_create_composition({
      name: "Intro Scene", width: 1920, height: 1080, duration: 5
    });
    ```
*   **Add Text:**
    ```javascript
    mcp_aftereffects_run_script({
      script: "createTextLayer",
      parameters: { compName: "Intro Scene", text: "My Title", fontSize: 150 }
    });
    ```
*   **Add a Shape:**
    ```javascript
    mcp_aftereffects_run_script({
      script: "createShapeLayer",
      parameters: { compName: "Intro Scene", shapeType: "rectangle", size: [500, 100], fillColor: [0, 0.5, 0.5] }
    });
    ```
*   **Change Text Font:**
    ```javascript
    mcp_aftereffects_run_script({
      script: "setLayerProperties",
      parameters: { compName: "Intro Scene", layerName: "My Title", fontFamily: "Verdana" }
    });
    ```
*   **Check Results:** (Needed after `run_script` calls to see success/error messages)
    ```javascript
    mcp_aftereffects_get_results({});
    ```

---

## ⚙️ Available Commands (Detailed)

The server provides control through these MCP tools:

### Dedicated MCP Tools

These are distinct tools your AI client might see.

*   `mcp_aftereffects_create_composition(name, width, height, ...)`: Creates a new composition. Provides clear parameters for the AI.
*   `mcp_aftereffects_get_results()`: Gets the outcome (success message or error details) of the *last* command sent via `run_script` or `create_composition`. Important for checking if things worked.
*   `mcp_aftereffects_get_help()`: Shows basic instructions.
*   `mcp_aftereffects_run_script(script, parameters)`: The most versatile tool. Executes one of the allowed ExtendScript functions defined in the bridge. See list below.

### Scripts via `run-script`

You use the `mcp_aftereffects_run_script` tool to call these, specifying the `script` name (string) and a `parameters` object.

*   `getProjectInfo`: Gets info about the AE project. (No parameters needed).
*   `listCompositions`: Lists compositions in the project. (No parameters needed).
*   `getLayerInfo`: Lists basic info (name, index, timing) for layers in the *currently active* composition. (No parameters needed).
*   `createComposition`: Alternative way to create a composition. (Params: `name`, `width`, `height`, `pixelAspect`, `duration`, `frameRate`, `backgroundColor`).
*   `createTextLayer`: Adds a text layer. (Params: `compName`, `text`, `position`, `fontSize`, `color`, `startTime`, `duration`, `fontFamily`, `alignment`).
*   `createShapeLayer`: Adds a shape layer. (Params: `compName`, `shapeType` ["rectangle", "ellipse", "polygon", "star"], `position`, `size`, `fillColor`, `strokeColor`, `strokeWidth`, `startTime`, `duration`, `name`, `points` [for polygon/star]).
*   `createSolidLayer`: Adds a solid or adjustment layer. (Params: `compName`, `color` [for solid], `name`, `position`, `size`, `startTime`, `duration`, `isAdjustment` [boolean]).
*   `setLayerProperties`: Modifies an existing layer. (Params: `compName`, `layerName` OR `layerIndex`, `position`, `scale`, `rotation`, `opacity`, `startTime`, `duration`, `text`, `fontFamily`, `fontSize`, `fillColor`).

---

## ❓ Troubleshooting

*   **Check AE Permissions:** Ensure `Allow Scripts to Write Files and Access Network` is enabled in AE Preferences.
*   **Bridge Panel Open?:** The `mcp-bridge-auto.jsx` panel *must* be open in After Effects. Check under the `Window` menu.
*   **Server Running?:** The `npm run start` command must be running in your terminal.
*   **Rebuild/Reinstall:** After changing *any* code (`.ts` or `.jsx`), you **must** run `npm run build` and potentially `node install-bridge.js` (if `mcp-bridge-auto.jsx` changed), then **restart** both the server and After Effects.
*   **Check Panel Logs:** The "Command Log" inside the AE bridge panel is your best friend! It shows received commands and errors happening within AE.
*   **Check Server Logs:** The terminal window where you ran `npm run start` will show server-side logs and errors.
*   **File Paths:** Double-check the path used in your MCP client configuration (if any). Ensure the AE executable path in `src/index.ts` is correct for your system (though the bridge panel method avoids needing this).
*   **Temp Folder Permissions:** Ensure the system's temporary folder is writable by After Effects.

---

## 🔒 Security

The system uses an "allow-list" (`allowedScripts` in `src/index.ts`) for the `run-script` tool. Only function names explicitly added to this list can be executed, preventing arbitrary code execution. Be cautious if you add new scripts, especially any that interact with the file system or network outside of the intended MCP communication.

---

## 🤝 Contributing

Improvements and new features are welcome!

### Development Setup

Follow Installation steps 2-5.

### Adding New Features

1.  **Write ExtendScript:** Add your new function (e.g., `myNewFunction(args)`) directly inside `src/scripts/mcp-bridge-auto.jsx`. Make sure it accepts an `args` object and returns a JSON string indicating success or error.
2.  **Register Function:** Add a `case "myNewFunction":` line inside the `switch` statement within the `executeCommand` function (in `mcp-bridge-auto.jsx`) to call your new function.
3.  **Allow Script:** Add the exact string `"myNewFunction"` to the `allowedScripts` array in `src/index.ts`.
4.  **Document:** Add your new script and its parameters to the "Available Tools & Scripts" section in this README.
5.  **Build/Install/Restart:** Run `npm run build`, `node install-bridge.js`, restart the server and AE.
6.  **Test!**

---

## 📜 License

This project is licensed under the MIT License. See the `LICENSE` file for details.
