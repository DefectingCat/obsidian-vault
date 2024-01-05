鸭子类型可以轻易的通过，而运行时的 `instanceof` 检查则只会检查其构造函数是否在原型链上。

```ts
const uint8 = new Uint8Array()
const arrBuffer = new ArrayBuffer(10)

function iNeedABffer(buffer: ArrayBufferLike) {}
function iNeedABfferTwo(buffer: Uint8Array) {}

iNeedABffer(uint8);
iNeedABffer(arrBuffer);

iNeedABfferTwo(uint8)
iNeedABfferTwo(arrBuffer)

console.log(uint8 instanceof Uint8Array)
console.log(uint8 instanceof ArrayBuffer)
```

![[Pasted image 20240105112409.png]]