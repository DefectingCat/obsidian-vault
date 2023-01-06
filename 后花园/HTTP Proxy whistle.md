## Document

https://wproxy.org/whistle/

## Install

```
npm i -g whistle
```

## Start

```
w2 start
```

## 配置方式

whistle有以下三种配置方式：

### 默认方式

默认是将匹配模式写在左边，操作uri写在右边

```
pattern operatorURI
```

whistle 将请求url与pattern匹配，如果匹配到就执行operatorURI对应的操作

### 传统方式

传统方式指的是传统的hosts配置方式，操作URI写在左边

```
operatorURI pattern
```

如果pattern为路径或域名，且operatorURI为域名或路径

```
www.test.com www.example.com/index.html
http://www.test.com www.example.com/index.html
```

这种情况下无法区分pattern和operatorURI，whistle不支持这种传统的方式，只支持默认方式

### 组合方式

传统hosts的配置对多个域名对于同一个ip可以采用这种方式：

```
127.0.0.1  www.test1.com www.test2.com www.testN.com
```

## 配置实例

转发 `aishow.aiyunhuizhan.com` 到 `127.0.0.1:3000`：

```
aishow.aiyunhuizhan.com http://127.0.0.1:3000
```

http://aishow.aiyunhuizhan.com/#/viewer?booth_id=74E21382-74C1-2377-B548-DB592D3B1B22