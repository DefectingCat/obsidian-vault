```tsx
type A = {
  name: 'a'
  age: number
}
type B = {
  name: 'b' | A['name']
} & Omit<A, 'name'>

interface A {
  name: 'a'
  age: number
}
interface B extends Omit<A, 'name'> {
  name: 'b' | A['name']
}
```