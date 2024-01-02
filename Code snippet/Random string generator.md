```tsx
const characters =
    'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789abcdefghijklmnopqrstuvwxyz!@#$%^&*_';

export function generateString(length: number) {
    let result = ' ';
    const charactersLength = characters.length;
    for (let i = 0; i < length; i++) {
        result += characters.charAt(
            randomIntFromInterval(0, charactersLength - 1)
        );
    }

    return result;
}
```