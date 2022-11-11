# Expose API in window with TypeScript

After load preload script. 

[Electron-forg load preload.ts](Electron-forg%20load%20preload%20ts%205f0eb85f2c994b9287f4a555f366c416.md)

It’s can be expose some methods to the browser global `Window` with `contextBridge` :

```tsx
import { contextBridge, ipcRenderer } from 'electron';

const windowAPI = {
  newWindow: async () => {
    ipcRenderer.invoke('window:new');
  },
};

export const API = {
  window: windowAPI,
};

contextBridge.exposeInMainWorld('api', API);
```

But before use it. The TypeScript need to the new type that we add in `Window`. So I chose to export `API` object. And create `src/@types/index.d.ts` :

```tsx
import { API } from '../../src/preload';

declare global {
  interface Window {
    api: typeof API;
  }
}
```

Now we can use it in render process.

```tsx
const handleClick = async () => {
  await window.api.window.newWindow();
};
```