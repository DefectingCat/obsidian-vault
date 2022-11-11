# Electron-forg load preload.ts

`src/preload.ts` :

```tsx
import { ipcRenderer } from "electron";
```

Add declare in `index.ts` and load preload script:

```tsx
declare const MAIN_WINDOW_PRELOAD_WEBPACK_ENTRY: string;

const createWindow = (): void => {
  // Create the browser window.
  const mainWindow = new BrowserWindow({
    height: 600,
    width: 800,
    webPreferences: {
      preload: MAIN_WINDOW_PRELOAD_WEBPACK_ENTRY,
      nodeIntegrationInWorker: true,
    },
  });

  // and load the index.html of the app.
  mainWindow.loadURL(MAIN_WINDOW_WEBPACK_ENTRY);

  // Open the DevTools.
  mainWindow.webContents.openDevTools();
};
```

And then add entry in `package.json` :

```json
"plugins": [
  [
    "@electron-forge/plugin-webpack",
    {
      "mainConfig": "./webpack.main.config.js",
      "renderer": {
        "config": "./webpack.renderer.config.js",
        "entryPoints": [
          {
            "html": "./src/index.html",
            "js": "./src/renderer.ts",
            "name": "main_window",
            "preload": {
              "js": "./src/preload.ts"
            }
          }
        ]
      }
    }
  ]
]
```