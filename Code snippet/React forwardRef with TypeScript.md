React forwardRef accept a ref as props. With typescript we can define refs type, and component's type.

```tsx
import { forwardRef } from "react";

type Props = {
  label?: string;
  id?: string;
  value?: string;
  onChange?: () => void;
};

const ForwardRefChild = forwardRef<HTMLInputElement, Props>(
  ({ label, id, value, onChange }, ref) => {
    const inputId = id ?? "test-id";

    return (
      <>
        <div>
          <label htmlFor={inputId}>{label}</label>
          <input ref={ref} id={inputId} value={value} onChange={onChange} />
        </div>
      </>
    );
  }
);

export default ForwardRefChild;
```