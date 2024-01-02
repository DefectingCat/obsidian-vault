Vitest 的 mock 方法可以 mock 任何第三方包，从而在测试时替换为特定的方法

```ts
import { existsSync, createReadStream, createWriteStream } from 'fs';
import { Readable, Writable } from 'stream';
import { afterEach, describe, it, vi } from 'vitest';

vi.mock('fs', async (importOriginal) => ({
  ...(await importOriginal<typeof import('fs')>()),
  existsSync: vi.fn().mockImplementation(() => true),
  createReadStream: vi.fn().mockImplementation(() => {
    const stream = new Readable();
    stream.push('test');
    stream.push(null);
    return stream;
  }),
  createWriteStream: vi.fn().mockImplementation(() => {
    class MockWriter extends Writable {
      _write(
        chunk: string[] /* chunk: any,
        encoding: BufferEncoding,
        callback: (error?: Error | null | undefined) => void, */,
      ): void {
        if (chunk.length <= 0) {
          throw new Error('no chunk received');
        }
        return;
      }
    }
    const stream = new MockWriter();
    return stream;
  }),
}));

vi.mocked(existsSync);
vi.mocked(createReadStream);
vi.mocked(createWriteStream);

describe('gzip', () => {
  afterEach(() => {
    vi.resetModules();
  });

  it('should gzip file', async () => {
    const { buildGzip } = await import('./builder');
    await buildGzip('');
  });
});
```