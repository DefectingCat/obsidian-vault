```tsx
type UseInput = [
  { value: string; onChange: (e: React.ChangeEvent<HTMLInputElement>) => void },
  () => void
];

const useInput = (): UseInput => {
  const [value, setValue] = useState('');
  const onChange = useCallback((e: React.ChangeEvent<HTMLInputElement>) => {
    setValue((e.target as HTMLInputElement).value);
  }, []);
  const resetField = useCallback(() => {
    setValue('');
  }, []);

  return [{ value, onChange }, resetField];
};
```