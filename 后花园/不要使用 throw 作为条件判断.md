在 `try...catch` 中有时候可能会尝试使用 `throw` 抛出错误，直接进入 `catch` 语句部分。从而代替条件判断。

但这并不是最佳实践，部分 IDE 可能会提示 `'throw' of exception caught locally`。

```ts
export const validateConfig = (config?: string) => {
    try {
        logger(`Starting parse config content.`);
        if (!config) throw new Error(`Config content is empty ${config}`);
        const content = JSON.parse(config);
        const result = v.validate(content, schema);
        console.log(result);
    } catch (e) {
        console.error(e);
        logger(`Parse config file error: ${e}.`);
    }
};
```

最佳实践是使用条件语句：

```ts
export const validateConfig = (config?: string) => {
    try {
        logger(`Starting parse config content.`);
        if (!config) return logger(`Config content is empty ${config}`);
        const content = JSON.parse(config);
        const result = v.validate(content, schema);
        console.log(result);
    } catch (e) {
        console.error(e);
        logger(`Parse config file error: ${e}.`);
    }
};
```