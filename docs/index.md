# Welcome to MkLorum

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

### 需求
- gain旋钮使用数控方式实现0-60dB的增益调节
- gain参数的级数为0-60dB共61级
- gain参数的控制协议可以是SPI或I2C
- 取消掉巨大的隔直电容，使用直流伺服电路进行offset补偿
- 使用AD作为数字界面
- 提供asio驱动用
- 使用mcu进行gain旋钮的增益控制
- 使用软件界面进行增益控制的调节
- 使用电位器进行增益控制的调节
- 增益控制时使用跨零侦测电路实现无噪声增益调节
- 提供符合标准的48V幻像供电

### 设计
电路拓扑架构使用由三个放大器构成的仪表放大器电路。电路的前端两个放大器提供大部分信号增益。
前端两个放大器使用由分立元件构成的电流负反馈放大器，实现最优的性能。前端电路的共模增益为1，后面连接的第三个放大器实现抑制共模信号的作用。
假设A1和A2的开环增益足够大，放大器的闭环时的中频增益（远高于由电容和电阻形成的高通滤波器的截止频率）Ad是：

$$
A_d=\frac{V_{out}}{V_{in}}=1+(2R_f/R_G)
$$

由外部信号或幻像供电引入的dc电压被耦合电容Cc滤除。有放大器A1和A2引入的输入偏移电压将被放大，由于放大器的增益高达60dB，1mV的偏移电压会产生1V的输出DC电压。这个输出偏移电压随增益的变化而变化，并且偏移电压的变化是可听见的。

### 隔直电容的引入
消除这种可听见的dc声音的方法是在反馈环路增加交流耦合电容切断直流反馈通路。此时差动偏移电压的增益Vout/Vos将不随增益调节而改变，变为单位值1。

由于引入了电容Cg，由差动偏移DC增益和差动信号增益共同构成的放大器的频响出现了一阶shelving高通滤波特性：

$$
f_p=\frac{1}{2\pi RgCg}
$$

极点位置出现在：

$$
f_z=\frac{1}{2\pi(R_g+2R_f)C_g}
$$

差分增益在DC是单位值1，然后在fz和fp之间每个10倍频程上升20dB。此方法对消除增益调节过程中出现的噪声十分有效。其缺陷是引入了电容的噪声和电容需要比较大的容量。

### DC伺服
为了解决隔直电容引入的问题，可以使用DC伺服电路代替隔直电容去维持DC偏移电压恒定。
DC伺服电路使用积分放大电路对放大器的输出进行检测输出控制电压已纠正产生的误差。假设积分放大电路具有足够高的开环增益，电路将保持放大器的DC输出偏移电压等于DC输入偏移电压。

### 频域效果
伺服电路的输出直接加载到放大器的输入，此时由输入电路产生的影响应该考虑进去。

$$
fo=\frac{1}{2\pi}\sqrt{\frac{A_d}{(R_d+\frac{R_s}{K})R_iC_cC_i}}
$$

$$
Q = \frac{1}{2\pi f_o(\frac{R_iC_i}{KA_d}+R_sC_c)}
$$

这里Ad = 1+(2Rf/Rg), 通带增益K=Rb/（Rb+Rd）。





---
参考资料：
SGM3005 DATASHEET
SGM4520 DATASHEET *
SGM4782 DATASHEET
AD5282  DATASHEET
AD5262  DATASHEET *
AD5293  DATASHEET
DC Servos and Digitally-Controlled Microphone Preamplifiers
THAT_5171_Datasheet
THAT626x Common-Mode Operation
Microphone pre-amp using a THAT 320 transistor array
AN IMPROVED MICROPHONE PREAMPLIFIER INTEGRATED CIRCUIT
# Markdown Cheat Sheet

Thanks for visiting [The Markdown Guide](https://www.markdownguide.org)!

This Markdown cheat sheet provides a quick overview of all the Markdown syntax elements. It can’t cover every edge case, so if you need more information about any of these elements, refer to the reference guides for [basic syntax](https://www.markdownguide.org/basic-syntax) and [extended syntax](https://www.markdownguide.org/extended-syntax).

## Basic Syntax

These are the elements outlined in John Gruber’s original design document. All Markdown applications support these elements.

### Heading

# H1
## H2
### H3

### Bold

**bold text**

### Italic

*italicized text*

### Blockquote

> blockquote

### Ordered List

1. First item
2. Second item
3. Third item

### Unordered List

- First item
- Second item
- Third item

### Code

`code`

### Horizontal Rule

---

### Link

[Markdown Guide](https://www.markdownguide.org)

### Image

![alt text](https://www.markdownguide.org/assets/images/tux.png)

## Extended Syntax

These elements extend the basic syntax by adding additional features. Not all Markdown applications support these elements.

### Table

| Syntax | Description |
| ----------- | ----------- |
| Header | Title |
| Paragraph | Text |

### Fenced Code Block

```
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
```

### Footnote

Here's a sentence with a footnote. [^1]

[^1]: This is the footnote.

### Heading ID

### My Great Heading {#custom-id}

### Definition List

term
: definition

### Strikethrough

~~The world is flat.~~

### Task List

- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

### Emoji

That is so funny! :joy:

(See also [Copying and Pasting Emoji](https://www.markdownguide.org/extended-syntax/#copying-and-pasting-emoji))

### Highlight

I need to highlight these ==very important words==.

### Subscript

H~2~O

### Superscript

X^2^

