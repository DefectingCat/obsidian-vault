## 展开运算符

展开运算符对数组和对象都是对于基本值的浅克隆。也就是说，数组中的基本值都会被克隆创建一个新的副本，而引用值依然保持以前的引用。

对于会修改数组本身的操作，可以利用浅克隆：

```tsx
const peaks = ['Tallac', 'Ralston', 'Rose'];
const [last] = [...peaks].reverse();

console.log(last);
```

同理，对于剩余参数也可以这样操作：

```tsx
function directions(...args: string[]) {
  const [start, ...remaining] = args;
  const [finish, ...stops] = remaining.reverse();

  console.log(`drive through ${args.length} towns`);
  console.log(`start in ${start}`);
  console.log(`the destination is ${finish}`);
  console.log(`stopping ${stops.length} times in between`);
}

directions('Truckee', 'Tahoe City', 'Sunnyside', 'Homewood', 'Tahoma');
```