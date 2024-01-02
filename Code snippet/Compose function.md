```tsx
const compose =
  <T>(...fns: ((...arg: T[]) => T)[]) =>
  (arg: T) =>
    fns.reduce((composed, fn) => fn(composed), arg);

const add = (n: number) => n + 1;
const multiply = (n: number) => n * 2;
const both = compose(add, multiply);

const result = both(20);

console.log(result);
```