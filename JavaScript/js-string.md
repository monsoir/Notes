# JavaScript 中字符串的一些认识

## 字符串的最大长度

- 字符串实际上可以看成是一个数组，因此字符串的长度收到了数组下标的影响
- 在 JavaScript 中，最大的正整数为 `Number.MAX_SAFE_INTEGER` = 9007199254740991 = 2^53 - 1 ≈ 9PB
- 因此字符串的最大长度为 2^53 - 1

## 字符编码

- 在 JavaScript 中，字符串都是基于 UTF-16 进行编码
    - UTF-16 是什么东西？就是 Unicode
- 在 Unicode 中，一个字符使用一个 Unicode 码点表示
- 一个 Unicode 码点就是 `U+????`, 其中 `????` 是使用十六进制表示的值
- Unicode 的**基本字符集**区域为 0-65535(16×16×16×16 -1 ), 即 `U+0000` - `U+FFFF`
    - 实际上，这个基本字符集就是重点，当我们需要操作的字符串，它们的码点都在基本字符集中时，那就相安无事，而需要引起注意的就是超出这个基本字符集的字符

## 超出基本字符集

看一个例子

```js
const a = 'a';
a.length; // 1

const dog = '🐶';
dog.length; // 2
```

同样是输入了一个字符而已，但是输出的长度并不相同。从上面知道，我们看到的字符，实际上在计算机中都存储为一个 Unicode 的码点；而当字符需要加载到内存时，又会变为另一种字节序列，这需要根据字符的编码规则而决定

因此，[先查一下](https://www.fileformat.info/info/unicode/char/search.htm)这两个字符的 Unicode 编码是什么

对于 `a`

- Unicode 码点为 `U+0061`, 在基本字符集中
- UTF-16 编码字节序列是 `0x0061`

对于 `🐶`(查询关键词为 dog face)

- Unicode 码点为 `U+1F436`, 超出了基本字符集
- UTF-16 编码字节序列是 `0xD83D 0xDC36`

可以看到，`a` 占用了一个字节，而 `🐶` 占用了两个字节，而我们知道，一个普通的字符占用的字节就是 1, 因此 `a.length` 为 1, 而 `dog.length` 为 2

---

额外，在 JavaScript 中，我们也可以通过不同的方法来创建字符

```js
console.log('🐶'); // 🐶 使用字面值创建
console.log('\u{1F436}'); // 🐶 \u{xxxx} 使用 Unicode 码点创建
console.log('\uD83D\uDC36'); // 🐶 \uXXXX\uXXXX 使用十六进制的值进行创建，且编码为 UTF-16
```

### 解决方法：将 UTF-16 编码转为码点后计算

当我们试图通过对字符串 `🐶` 进行长度统计时，`'🐶'.length` 给出的结果是 `2`, 但是这从人类的角度来说，是不对的，应该是 `1`

这是由于在统计长度时，计算的基础是 UTF-16 编码，而 `🐶` 占用了两个字节，所以返回了 2, 因此我们需要将 `🐶` 转成 Unicode 码点后进行计算，即将字符串分拆为字符数组

```js
const dog = '🐶';
dog.length; // 2
[...dog].length; // 1

const dogs = '🐶🐶🐶';
dogs.length; // 6
[...dogs].length; // 3
```

## 合成字符

除了上面的情况，还有一些外国文字，如 `é`, 它的显示，实际上还可以通过合成字符来进行的

对于 `é`, 有两种方式表示

- 单一 Unicode 码点 `U+00E9`
- 合成 Unicode 码点，`U+0065` + `U+0301`, 即 `e` + `´`

可以在 Node 中查看一下输出

```js
console.log('\u00E9'); // é
console.log('\u0065\u0301'); // é

'\u00E9' === '\u0065\u0301' // false

'\u00E9'.length // 1
'\u0065\u0301'.length // 2
```

可以看出，输出字形相同的字符，使用单一码点和使用合成码点进行比较时，却不相等，这很可能会导致想不到的 bug

### 解决办法：字符串归一化 Normalizing strings

那问题来了

- 对于合成字符，又怎么知道它什么时候是合成码点，什么时候是单一码点？

实际上，有一个解决方案，字符串范式转换。字符串范式(Canonical form)有四种：

- NFC Normalization Form Canonical Composition
    - 先假定字符串中的字符的码点都已经拆开，因此处理时需要合成各个码点
    - 如，对于 `\u0065\u0301`, 处理时合成为 `\u00E9`
- NFD Normalization Form Canonical Decomposition
    - 先假定字符串中的字符的码点都已经合成了，因此处理时需要拆开各个码点
    - 如，对于 `\u00E9`, 处理时，拆分为 `\u0065\u0301`
- NFKC Normalization Form Compatibility Composition
- NFKD Normalization Form Compatibility Decomposition

对于 JavaScript, ES6 中提供了方法 `String.prototype.normalize([form])` 来进行字符串的归一化操作，参数 `form` 默认为 `NFC`

```js
const longOne = '\u0065\u0301';
const shortOne = '\u00E9';

longOne === shortOne; // false
shortOne === longOne.normalize('NFC'); // true
longOne === shortOne.normalize('NFD'); // true

longOne.normalize('NFC').length; // 1
shortOne.normalize('NFD').length; // 2
```


## References

- [fileformat 查询 Unicode 编码](https://www.fileformat.info/info/unicode/char/search.htm)
- [When "Zoë" !== "Zoë". Or why you need to normalize Unicode strings](https://withblue.ink/2019/03/11/why-you-need-to-normalize-unicode-strings.html)



