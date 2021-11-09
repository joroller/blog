# package.json中依赖库的版本号介绍

## 使用举例

```json
"devDependencies": {
    "tslint": "^5.20.1",
    "typescript": "^2.9.2"
},
"dependencies": {
	"jquery": "^3.6.0",
	"underscore": "1.13.1"
},
```

## 版本格式

**格式**：
```json
"依赖库名称": "MAJOR.MINOR.PATCH"
```

**举例**：
```json
"jquery": "3.6.0",
```

依赖库名称为jquery，  MAJOR为3，  MINOR为12， PATCH为1

**说明**：

MAJOR：这个版本号变化了表示有了一个不可以和上个版本兼容的大更改。

MINOR：这个版本号变化了表示有了增加了新的功能，并且可以向后兼容。

PATCH：这个版本号变化了表示修复了bug，并且可以向后兼容。

## ~ 与 ^ 符号

### 波浪符号（~）：

**说明**：
匹配版本时固定 MAJOR 及 MINOR 版本号，PATCH 版本可动态变更。

**举例**：
```json
"jquery": "~3.6.0",
```
可以匹配 3.6.0 <= 匹配版本 < 3.7.0

### 插入符号（^）：

匹配版本时固定 MAJOR 版本号，MINOR 及 PATCH 版本可动态变更

**举例**：
```json
"jquery": "^3.6.0",
```
可以匹配 3.6.0 <= 匹配版本 < 4.0.0


## 参考

[Node.js中package.json中库的版本号详解(^和~区别)](https://blog.csdn.net/njweiyukun/article/details/70309066)
