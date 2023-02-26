TypeScript 本质上属于鸭子类型（duck typed）：如果你向函数传递具有所有正确属性的值，那么它就不会在乎如何创建该值。

> 如果它长的像鸭子，叫起来也像鸭子，那它就是鸭子。

换句话说，函数签名是接受其子类型的：

```tsx
interface Vector2D {
  x: number;
  y: number;
}

function calculateLength(v: Vector2D) {
  return Math.sqrt(v.x * v.x + v.y * v.y);
}

interface NamedVector extends Vector2D {
  name: string;
}

const myV: NamedVector = {
  name: 'xfy',
  x: 10,
  y: 20,
};

console.log(calculateLength(myV));
```

`NamedVector` 是扩展自其父类型 `Vector2D` 所以也可以将其实例传递给函数 `calculateLength` 。

但这也会导致一些问题，由于 `Vector3D` 是 `Vector2D` 的子类型，所以 `Vector3D` 也可以传递给 `calculateLength` 方法。而该方法并不会去计算三维向量，它是为二维向量所设计的。

```tsx
interface Vector3D extends Vector2D {
  z: number;
}

function normalize(v: Vector3D) {
  const length = calculateLength(v);
  return {
    x: v.x / length,
    y: v.y / length,
    z: v.z / length,
  };
}
```

这是类型检查的疏忽吗？其实并不是。由于鸭子类型的特性，函数的参数都是开放的，虽然我们为其参数指定了某个接口或类型别名，但它实际上可能会有别的属性。

例如，当我们为参数签名后，我们再去遍历这个参数。虽然它明确的签名了几个属性，但因为鸭子类型的特点，尽管签名的参数只有 `x` 、`y` 和 `z` 这三个属性。所以理论上来说 `coord` 应该只会是 number 类型，但 `coord` 还是具有 any 类型。因为我们无法确定参数 `v` 具体由多少个属性。

```tsx
function myCalculate(v: Vector3D) {
  for (const axis of Object.keys(v)) {
    // const coord: any
    const coord = v[axis]
  }
}
```

也就是说我们传递的参数也可能是这样的：

```tsx
const myV = {
  x: 10,
  y: 20,
  z: 30,
  foo: 'bar',
};
```

这时候就没办法准确确定 `coord` 的类型。