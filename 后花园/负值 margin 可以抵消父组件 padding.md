将子组件的 margin 设置为负值时，子组件会向 margin 反方向位移。

```scss
.right {
  background: #ffffff;
  border-radius: 12px;
  flex: 1;
  padding: 15px;

	.slogo {
    width: 114px;
    height: 39px;
    background: linear-gradient(274.41deg, #9330e5 2.11%, #1067f7 106.52%);
    border-radius: 0px 50px 50px 0px;
    margin-left: -15px;
    font-size: 18px;
    line-height: 27px;
    color: #ffffff;
    display: flex;
    align-items: center;
    padding: 0 15px;
  }
}
```
