`Array.prototype.fill()` just execute once. The entire array will be the same value. 

ES6 `Array.from()` function receive secondary parameter, it will be execute each times.

```tsx
const defaultColors = Array.from({ length: 6 }, randomColors)
```