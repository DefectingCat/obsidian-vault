TypeScript 类型虽然不能直接作用于 JavaScript 的值层面，但是它可以在 tsc 的帮助下实现一个静态的类似于运行时的检查。

例如使一个函数根据所传递的参数来决定其返回值，这通常是利用范型来解决的问题。但不光光是返回所传递的范型。TypeScript 还能根据范型的实际的类型来做条件判断，其写法类似于三元操作符 `largeData: T extends false ? LargeData : null;` 。

```ts
export type LargeData = string;
export interface BoothData<T> {
  boothId: string;
  boothName: string;
  largeData: T extends false ? LargeData : null;
}
export interface Booth<T> {
  data: BoothData<T>;
}

const getBooth = async <T extends boolean>(
  ID: string,
  cache: T
): Promise<Booth<T>> => {
  //   const result = await ax.post(server.addr, { id: ID });
  //   return result.data;
  return {} as Booth<T>;
};

const booth = await getBooth("test1", false);
validate(booth.data.largeData);
const booth2 = await getBooth("test2", true);
validate(booth2.data.largeData);

function validate(params: LargeData) {}
```