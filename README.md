# electron-sender
A simple tool that lets you interact between processes in an ElectronJS app.

## Quickstart
To install:
```cmd
> npm i electron-sender
```

_or_
```cmd
> npm install electron-sender
```

_For developement environments, please use the `--save-dev` flag when installing._

To use:
##### main.js
```javascript
const { app, BrowserWindow } = require("electron");
const Sender = require("electron-sender");

app.on("ready", () => {
  const mainWindow = new BrowserWindow({ ... });
  mainWindow.webContents.loadFile(__dirname + "/index.html");
  Sender.append(mainWindow);
  Sender.receive(mainWindow, "test_function", (param1, param2) => {
    console.log("Received param1: " + param1 + "\nReceived param2: " + param2);
  });
});
```

##### index.html
```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      Sender.send("test_function", ["string1", "string2"]);
    </script>
  </body>
</html>
```

And the output:
```text
> Launch app
  > Window opens
  > Console message
    > Received param1: String  "string1"
    > Received param2: String  "string2"
```

## Documentation
### Definitions
> #### `Sender.receive` - function receive(process: Electron.BrowserWindow)

> #### `Sender.send` (main process) - function send(name: String, params: Array)

> #### `Sender.send` (renderer process) - function send(name: String, params: Array)

> #### `Sender.append` - function append(process: Electron.BrowserWindow)

### What they do
> #### `Sender.receive()` - defines a function to run

> #### `Sender.send()` - runs a defined function (used in renderer and main processes)

> #### `Sender.append()` - appends the tools to the selected process

### How it works
Messages between processes are very simple.

Messages from `main.js` to `index.html` occur when a default function built-in called `WebContent.executeJavascript` executes a function defined in the renderer process (index.html).

Example:
##### # main.js
```javascript
mainWindow.webContents.executeJavascript(`Sender.functions["test_function"]("string1", "string2")`);

// Is the same as

Sender.send("test_function", ["string1", "string2"]);
```
##### # index.html
```html
<script>
Sender.functions["test_function"] = (param1, param2) => {
  console.log(param1, param2);
};

// Is the same as

Sender.receive("test_function", (param1, param2) => {
  console.log(param1, param2);
});
</script>
```

This process works the same way, `send` to `receive` in the main process, `send` to `receive` in the renderer process, Vice-Versa.

### What certain functions do
#### __Appending a process using `Sender.append`__
This function prepares a certain ElectronJS process (window) for this package.

Basically it just gives the window the tools you need to interact between processes.

#### __Sending messages or functions using `Sender.send`__
This function works with both __main__ and __renderer__ processes, as well as `Sender.receive`.

This will listen for the specified message and run the defined function by `Sender.receive`.

#### __Receiving messages using `Sender.receive`__
Like mentioned, both `send` and `receive` functions are necessary for interaction.

This function is very important because you need a function to run using `Sender.send`, so without this, the package is useless.

## Errors
> ### Uncaught Error: Process "" is undefined
&nbsp;&nbsp;&nbsp;&nbsp;This error occurs when using `Sender.send` could not find the function name defined by `Sender.receive`

## License
__MIT__