当在父元素上监听 drag 事件时，当拖动到其子元素上（例如带有文本节点的元素）可能会导致触发 `dragleave` 事件，从而导致事件顺序不正确。

这时候可以使用 CSS `pointer-events: none;` 来将子元素标记为不会成为鼠标事件的 target，即可保证父元素依然触发事件。

```tsx
<div
  className={clsx(
    'bg-white rounded-lg p-3',
    'max-w-lg transition-all h-80',
    'flex',
    dragging && '!p-5 bg-gray-50'
  )}
  onDrag={handleDrag}
  onDragStart={handleDrag}
  onDragEnter={handleDrag}
  onDragOver={handleDrag}
  onDragEnd={handleLeave}
  onDragLeave={handleLeave}
  onDrop={handleLeave}
>
  <div
    className={clsx(
      'rounded-lg border',
      'border-dashed border-2',
      'flex-1 flex justify-center items-center',
      'pointer-events-none'
    )}
  >
    <input
      ref={ref}
      type="file"
      accept="image/*"
      onChange={handleChange}
      id="upload"
      name="upload"
      className="hidden"
    />
    <div>
      <div className={clsx('select-none', dragging && 'hidden')}>
        <span
          onClick={handleUpload}
          className={clsx('font-bold cursor-pointer')}
        >
          Choose a file
        </span>{' '}
        <span>or drag it here.</span>
      </div>
      <div className={clsx('hidden', dragging && '!block')}>
        Drop to upload.
      </div>
    </div>
    <img src={url} alt="" />
  </div>
</div>
```