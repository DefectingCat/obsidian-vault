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