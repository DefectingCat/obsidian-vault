```ts
if (
  typeof error === 'object' &&
  error !== null &&
  'errorCode' in error &&
  (error as Record<string, unknown>).errorCode === 1109001
) {
  notification.error({
    message: 'send failed',
    description: 'send too quickly',
  });
}

```