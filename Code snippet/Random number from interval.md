`Math.random()` multiply max value minus min value plus one and plus min value.

```tsx
export function randomIntFromInterval(min: number, max: number) {
    // min and max included
    return Math.floor(Math.random() * (max - min + 1) + min);
}
```