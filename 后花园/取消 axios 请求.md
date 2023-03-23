新的 axios 文档的 [cancellation](https://axios-http.com/docs/cancellation) 中，可以使用 `AbortController` 来 abort 一个请求。

```ts
export const characterController = new AbortController();

type SpeechToText = {
  data: string;
} & BaseCode;
/**
 * 将录音到到音频二进制文件识别为文字
 *
 * 该接口有文件拓展名验证，需要设置上传到文件带有 pcm 拓展名
 * @param audio
 */
export const speechToText = async (audio: Blob) => {
  const form = new FormData();
  const file = new File([audio], 'speech.pcm');
  form.append('recording', file);

  const result = await $ax.post<SpeechToText>(server.speechToText, form, {
    signal: characterController.signal,
  });
  return result?.data?.data;
};
```

使用 `AbortController` abort 一个请求时，该请求会抛出 `CanceledError`：

```ts
    // 取消上次识别中的请求
    if (recognizing) {
      characterController.abort('holding');
      setRecognizing.setFalse();
    }

    // 识别结果
    try {
      const txt = await speechToText(audio);
      txt ? setText(txt) : setText('识别失败');
      console.log(`虚拟人物：${txt}`);
      setRecognizing.setFalse();
      await recorder.current.destroy();
      return txt;
    } catch (error) {
      if (error instanceof CanceledError) {
        isDev && console.log('handleRecognize canceled');
      } else {
        console.error(error);
      }
    }
```