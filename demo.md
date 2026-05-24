# Creating a Cirbi MCP Server

This is a small demo of how to create an MCP server and understand its basic logic.

It uses **Cirbi** - a fictional character intentionally misspelled (Cirbi instead of Kirby). The goal is to slightly confuse the LLM and ensure it does not rely on prior knowledge when reasoning about it.

![Kirbi-ai](https://raw.githubusercontent.com/mhornstein/Cirbi-Demo/main/images/cirbi-ai.PNG)

## Prerequisites

Before starting the workshop, please install the following:

1. Install Python
   [Python Download](https://www.python.org/downloads/?utm_source=chatgpt.com)

2. Install Claude Desktop
   [Claude Desktop Download](https://claude.com/download?utm_source=chatgpt.com)

The installation is simple and clear - like any other software.

---

## Step 1: Create a simple Python tool

Create a simple text file named `cirbi.py` on your computer with the following content:

```python
def cirbi(gesture: str, times: int):
    '''
    Return repeated Cirbi-style text based on the chosen gesture and number of repeats.
    '''
    if gesture == "hug":
        return "(>^.^)> " * times
    elif gesture == "dance":
        return "v(-'.'-)^ " * times
    else:
        return "(>'.')> ?"


if __name__ == "__main__":
    print(cirbi("hug", 3))
    print(cirbi("dance", 2))
    print(cirbi("smile", 2))
```

To run the tool, open your CMD and type:

```bash
python <path_to_the_file>
```

Example:

```bash
python C:\Users\User\Desktop\cirbi.py
```

---

## Step 2: Transform your Python tool into an MCP server

Update the content of the file as follows:

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("Cirbi server")


@mcp.tool()
def cirbi(gesture: str, times: int):
    '''
    Return repeated Cirbi-style text based on the chosen gesture and number of repeats.
    '''
    if gesture == "hug":
        return "(>^.^)> " * times
    elif gesture == "dance":
        return "v(-'.'-)^ " * times
    else:
        return "(>'.')> ?"


if __name__ == "__main__":
    mcp.run()
```

---

## Step 3: Install the MCP library

Open your CMD and run:

```bash
pip install mcp
```

---

## Step 4: Run the server

To run the server, open your CMD and type:

```bash
python <path_to_the_file>
```

Example:

```bash
python C:\Users\User\Desktop\cirbi.py
```

The CMD will appear blank and wait for input. You can interact with the server using JSON-RPC commands.

---

### 1. "Introduce yourself" to the server

```json
{"jsonrpc":"2.0","id":0,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"example-client","version":"1.0"}}}
```

When the server responds, confirm you're ready by sending:

```json
{"jsonrpc":"2.0","method":"notifications/initialized"}
```

---

### 2. Ask the server to list its tools

```json
{"jsonrpc":"2.0","id":1,"method":"tools/list"}
```

---

### 3. Run the cirbi tool

```json
{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"cirbi","arguments":{"gesture":"hug","times":3}}}
```

```json
{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"cirbi","arguments":{"gesture":"dance","times":3}}}
```

```json
{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"cirbi","arguments":{"gesture":"smile","times":2}}}
```

---

## Step 5: Try to communicate with Cirbi via Claude Desktop

Try asking Claude to use Cirbi. For example:

* ```can you make cirbi hug 3 times?```
* ```can you make cirbi dance twice?```

You will see that it does not understand what you mean yet. 

Note that there is also no "cirbi connector" available at this stage (only the "Add Connectors" option is avaiable):

![No Cirbi connector](https://raw.githubusercontent.com/mhornstein/Cirbi-Demo/main/images/cladue-no-connectors.PNG)

---

## Step 6: Configure Claude Desktop to talk to the Cirbi MCP

To update the config:

1. Open Claude Desktop.
2. File → Settings.
3. Open Developer.
4. Click **Edit Config**.
5. Add the following (and save):

```json
{
  "mcpServers": {
    "cirbi": {
      "command": "python",
      "args": ["C:\\Users\\User\\Desktop\\cirbi.py"]
    }
  },
  "preferences": {
    "coworkScheduledTasksEnabled": false,
    "ccdScheduledTasksEnabled": false,
    "sidebarMode": "chat",
    "coworkWebSearchEnabled": true,
    "floatingAtollActive": true
  }
}
```

5. Restart Claude Desktop (right click → Quit, and start again).

---

## Step 7: Try to communicate with Cirbi via Claude Desktop again

You can see now that the cirbi connector appears and can be turn on.

![Claude with Cirbi connector](https://raw.githubusercontent.com/mhornstein/Cirbi-Demo/main/images/claude-with-connectors.PNG)

Try in chat:

* ```can you make cirbi hug 3 times?```
* ```can you make cirbi dance twice?```
* ```please make cirbi dance based on the number of sides in a triangle.```
* ```please make cirbi affectionate based on the number of continents we have.```
* ```please make cirbi hub based on the number of continents we have``` (note the typo in "hub"!)

### 💡 Tip

Try running the same prompt using both Haiku and Sonnet models to see how their outputs differ in real time. 
Haiku is usually faster and more concise, while Sonnet tends to give deeper and more structured responses.

---

## End of Demo

You now have a working example of how an MCP server can expose a simple tool and how an LLM can interact with it via Claude Desktop.
