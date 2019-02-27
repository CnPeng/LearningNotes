[随书源码下载页面](https://nostarch.com/teachkids)

> CnPeng 在运行和编写代码时使用了 PyCharm , 没有按照书中直接使用 IDLE。

## 一、认识环境

* 效果图1

![](https://s2.ax1x.com/2019/02/08/kNSC2d.png)

* 代码1

```python
import turtle

colors = ['red', 'purple', 'blue', 'green', 'yellow', 'orange']
turtle.bgcolor('black')
t = turtle.Pen()

for x in range(360):
    t.pencolor(colors[x % 6])
    t.forward(x)
    t.width(x / 100 + 1)
    t.left(61)
```

* 效果图2

![](https://images.gitee.com/uploads/images/2019/0208/111239_c4d7ff52_930142.png "屏幕截图.png")

* 代码2

```python
name = input("请输入您的姓名\n")
print("Hello，", name)
```

* 效果图3

![](https://images.gitee.com/uploads/images/2019/0208/111448_467a432b_930142.png "屏幕截图.png")

* 代码3

```python
name = input("请输入您的姓名\n")
print("Hello, ", name, name, name, name, name)
```
---

## 二、用Python绘图

### 1、绘制正方形迷宫

![](https://images.gitee.com/uploads/images/2019/0208/111952_af218a82_930142.png "屏幕截图.png")

```python
import turtle

t = turtle.Pen()
for x in range(100):
    t.forward(x)
    t.left(90)
```

* python 中 `#` 表示注释


### 2、旋转的迷宫

![](https://images.gitee.com/uploads/images/2019/0208/112337_ba797db4_930142.png "屏幕截图.png")

```python
import turtle

t = turtle.Pen()
for x in range(100):
    t.forward(x)
    t.left(91)
```

与前面的正方形图案相比，此处仅是改变了 left 的角度值

* forward 表示画线段
* left 表示向左偏移（展现出来的效果就是逆时针旋转） 

### 3、画圆

#### (1)、正圆——cirlce()

![](https://images.gitee.com/uploads/images/2019/0208/112938_54d7f1f0_930142.png "屏幕截图.png")

```python
import turtle

t = turtle.Pen()
for x in range(100):
	 # circle(半径) 参数为半径值
    t.circle(x)
```

#### (2)、多个圆


![](https://s2.ax1x.com/2019/02/08/kNSPxA.png)

```python
import turtle

t = turtle.Pen()
for x in range(100):
    t.circle(x)
    t.right(90)
```
#### (3)、具有螺旋效果的多个圆

![](https://s2.ax1x.com/2019/02/08/kNSpPe.png)

```python
import turtle

t = turtle.Pen()
for x in range(100):
    t.circle(x)
    t.left(91)

```
### 4、改变画笔颜色
#### (1)、改变画笔颜色
通过 `penColor('red')` 可以改变画笔颜色为红色

![](https://s2.ax1x.com/2019/02/08/kNS6IO.png)

```python
import turtle

t = turtle.Pen()
t.pencolor('red')
for x in range(100):
    t.circle(x)
    t.left(91)
```
#### (2)、四色螺旋线

![](https://s2.ax1x.com/2019/02/08/kNpPFU.png)

```python
import turtle

colors = ['red', 'yellow', 'green', 'blue']
t = turtle.Pen()

for x in range(100):
    t.pencolor(colors[x % 4])
    t.forward(x)
    t.left(91)
```


#### (3)、改变背景色——turtle.bgcolor()

![](https://s2.ax1x.com/2019/02/08/kNp3SH.png)

```python
import turtle

colors = ['red', 'yellow', 'green', 'blue']
turtle.bgcolor('black')
t = turtle.Pen()

for x in range(100):
    t.pencolor(colors[x % 4])
    t.forward(x)
    t.left(91)
```
#### (4)、四色螺旋圆

![](https://s2.ax1x.com/2019/02/08/kNpwtS.png)

```python
import turtle

colors = ['red', 'yellow', 'green', 'blue']
turtle.bgcolor('black')
t = turtle.Pen()

for x in range(100):
    t.pencolor(colors[x % 4])
    t.circle(x)
    t.left(91)
```



### 5、动态调整边数side
#### (1)、调整边数获取不同的效果

##### A: 示例1

```python
import turtle

colors = ['red', 'yellow', 'green', 'blue', 'orange', 'purple']
turtle.bgcolor('black')
t = turtle.Pen()

# 调整sides 的值，看得到的效果图
sides = 2

for x in range(360):
    t.pencolor(colors[x % sides])
    t.forward(x)
    t.left(360 / sides + 1)
```

当 sides 依次取值 2、3、4 时效果如下：

![kN9TPS.png](https://s2.ax1x.com/2019/02/08/kN9TPS.png)

![kNC9GF.png](https://s2.ax1x.com/2019/02/08/kNC9GF.png)

![kNCFM9.png](https://s2.ax1x.com/2019/02/08/kNCFM9.png)

![kNC3qI.png](https://s2.ax1x.com/2019/02/08/kNC3qI.png)

![kNCJdP.png](https://s2.ax1x.com/2019/02/08/kNCJdP.png)



#### (2)、用户动态输入边数

input() 表示让用户输入内容
eval() 表示将输入转为数值

```python
import turtle

colors = ['red', 'yellow', 'green', 'blue', 'orange', 'purple']
turtle.bgcolor('black')
t = turtle.Pen()
sides = eval(input("输入边数，取值2-6：\n"))

for x in range(360):
    t.pencolor(colors[x % sides])
    # t.forward(x * 3 / sides + x)
    t.forward(x)
    t.left(360 / sides + 1)
```
#### (3)、绘制锐角图案

![kNCsZq.png](https://s2.ax1x.com/2019/02/08/kNCsZq.png)

```python
import turtle

colors = ['red', 'yellow', 'green', 'blue', 'orange', 'purple']
turtle.bgcolor('black')
t = turtle.Pen()
sides = eval(input("输入边数，取值2-6：\n"))

for x in range(360):
    t.pencolor(colors[x % sides])
    t.forward(x)
    t.left(165)
```

---
## 三、数字和变量

### 1、变量

命名规则：字母数字下划线，区分大小写

### 2、整数和浮点数

操作符|含义|示例|结果
----|----|----|----
\+ | 加法 | 2+2 | 4
- | 减法 | 2-1 | 1
\* | 乘法 | 2*2 | 4
/ | 除法（真除）| 5/2 | 2.5
// | 除法（整除）| 5//2 | 2
\*\* | 幂 | 4\*\*2|16 


python 中的除法是真除，得到的结果会带有小数部分，这与其他语言不同。

### 3、字符串

```python 
name = input("请输入您的名字\n")
for x in range(10):
    print("名字:", name, end="丰！")
```

![kNQj1A.png](https://s2.ax1x.com/2019/02/08/kNQj1A.png)

上述示例中的 print 语句中 使用 end 定义了print语句的结束符号，这里使用 ”丰！“ 替代了默认的换行符。

### 4、画以字符串为元素的螺旋图形

![kN8p0x.png](https://s2.ax1x.com/2019/02/08/kN8p0x.png)

![kN3Xp4.png](https://s2.ax1x.com/2019/02/08/kN3Xp4.png)

```python
import turtle

pen = turtle.Pen()
colors = ['red', 'green', 'blue', 'yellow', 'orange', 'purple']

turtle.bgcolor('black')
name = turtle.textinput("提示", "请输入你的名字")

for x in range(360):
    pen.penup()
    pen.color(colors[x % 6])
    pen.forward(x * 4)
    pen.left(92)
    pen.pendown()
    pen.write(name, font=('Arial', int((x+4)/4), 'bold'))
```

上述代码中：

`turtle.textinput()` 的作用就是弹出一个弹窗，提示用户输入文本。第一个参数表示标题，第二参数为描述内容

`pen.penup()`表示抬起画笔，`pen.pendown()`表示落下画笔

`pen.write()`表示开始绘制指定的内容。


### 5、列表

列表就是被`[]` 包裹，用逗号间隔的多个相同元素的组合。

前面使用的 `colors=['red','green']` 就是一个列表

示例代码：

```python
import turtle

pen = turtle.Pen()
colors = ['red', 'green', 'blue', 'yellow', 'orange', 'purple', 'white', 'gray']

turtle.bgcolor('black')
sides = int(turtle.numinput("提示", "请输入边数：1-8", 4, 1, 8))
name = turtle.textinput("提示", "请输入你的名字")

for x in range(360):
    pen.penup()
    pen.color(colors[x % sides])
    pen.forward(x * 2)
    pen.left(360 / sides + 2)
    pen.pendown()
    pen.write(name, font=('Arial', int((x + sides) / sides), 'bold'))
```
 
运行效果：

![kNGCKs.png](https://s2.ax1x.com/2019/02/08/kNGCKs.png)

![kN8p0x.png](https://s2.ax1x.com/2019/02/08/kN8p0x.png)

![kNGE5T.png](https://s2.ax1x.com/2019/02/08/kNGE5T.png)

上述示例代码中：

`turtle.numinput()` 表示输入数组，第一个参数为标题，第二个参数为描述文本，第三个参数为默认值，第四个参数为最小取值，第五个参数为最大取值

`int()` 表示将内容转换为整数值 


### 6、练习

```python
problem = input("请输入算数表达式 或 输入 q 结束\n")

while (problem != 'q'):
    print(problem, "的值为:", eval(problem))
    problem = input("请输入算数表达式 或 输入 q 结束\n")
```

运行结果：

```
请输入算数表达式 或 输入 q 结束
3+4
3+4 的值为: 7
请输入算数表达式 或 输入 q 结束
```

---

## 四、循环

### 1、构建自己的循环

#### (1)、range

```python
print(range(5))
print(list(range(5)))
```
输出结果：

```
range(0, 5)
[0, 1, 2, 3, 4]
```

#### (2)、6个花瓣

![](https://s2.ax1x.com/2019/02/11/kaMexA.png)

```python
import turtle

pen = turtle.Pen()

for x in range(6):
    pen.circle(100)
    pen.left(60)
```

### 2、让用户指定花瓣数量
#### (1)、用户指定花瓣数量

![](https://s2.ax1x.com/2019/02/11/kaM8Pg.png)

```python
import turtle

pen = turtle.Pen()
# 弹窗提示用户输入花瓣数量，默认值6
nums = int(turtle.numinput("提示", "请输入花瓣数量", 6))

for x in range(nums):
    pen.circle(50)
    pen.left(360/nums)
```

#### (2)、两层的彩色花瓣

![](https://s2.ax1x.com/2019/02/11/kaQSJg.png)

```python
import turtle

pen = turtle.Pen()
nums = int(turtle.numinput("提示", "请输入花瓣数量:", 6))

turtle.bgcolor('black')
pen.width(2)

#改变画笔的绘制速度
pen.speed(3)

for x in range(nums):
    # 实际上绘制了两次，先绘制一个红色的大圆，再绘制一个黄色小圆，总共60个圆
    pen.color('red')
    pen.circle(100)
    pen.color('yellow')
    pen.circle(50)
    pen.left(360/nums)
```

### 3、while循环

![](https://s2.ax1x.com/2019/02/11/kaQf6s.png)

```python
name = input("请输入名字：")
while name != "q":
    for x in range(20):
        print(name, end=" ")

    print()
    name = input("请输入名字，或者输入 q 退出:")

print("while循环结束了")
```


### 4、姓名螺旋线

* `listA=[]` 表示定义一个空的列表，使用 `append(xx)` 追加元素
* `len(列表)` 获取元素个数

![](https://s2.ax1x.com/2019/02/11/kal6D1.png)

```python
import turtle

# 预定义一个空的列表
students = []

name = turtle.textinput("提示", "请输入名字，或者输入 q 退出")
while name != "q":
    students.append(name)
    name = turtle.textinput("提示", "请输入名字，或者输入 q 退出")

colors = ['red', 'green', 'blue', 'yellow', 'white', 'purple', 'orange']
turtle.bgcolor('black')
pen = turtle.Pen()

for x in range(100):
    # 获取列表的长度时使用的是 len 函数
    pen.color(colors[x % len(colors)])
    pen.penup()
    pen.forward(x * 2.5)
    pen.pendown()
    pen.write(students[x % len(students)], font=("Arial", 8, "bold"))
    pen.left(360 / len(students) + 2)
```

### 5、循环嵌套

* turtle 对象的 `position()` 函数可以获取当前画笔的位置，包含 x, y 坐标
* turtle 对象的 `heading()` 函数可以获取当前画笔的角度位置，取值 0.0-360.0


#### (1)、示例1

![](https://s2.ax1x.com/2019/02/11/kaJAm9.png)

```python
import turtle

pen = turtle.Pen()
pen.penup()
turtle.bgcolor('black')
pen.speed(10)

colors = ['red', 'green', 'blue', 'yellow', 'white', 'purple', 'orange']
sides = int(turtle.numinput("提示", "请输入边数", 4, 1, 8))

for x in range(100):
    pen.forward(x * 4)
    # 记录位置，包含 x,y 坐标
    position = pen.position()
    # 记录角度
    heading = pen.heading()

    for y in range(int(x / 2)):
        pen.pendown()
        pen.pencolor(colors[int(y % sides)])
        pen.forward(y * 2)
        pen.right(360 / sides -2)
        pen.penup()

    pen.setx(position[0])
    pen.sety(position[1])
    pen.setheading(heading)
    # pen.color(colors[x % len(colors)])
    pen.left(360 / sides + 2)
```

#### (2)、示例2

![](https://s2.ax1x.com/2019/02/11/kaBIBT.png)

```python
import turtle

pen = turtle.Pen()
# pen.penup()
turtle.bgcolor('black')
pen.speed(0)

colors = ['red', 'green', 'blue', 'yellow', 'white', 'purple', 'orange']
sides = int(turtle.numinput("提示", "请输入边数", 4, 1, 8))

for x in range(100):
    pen.forward(x * 4)
    # 记录位置，包含 x,y 坐标
    position = pen.position()
    # 记录角度
    heading = pen.heading()

    for y in range(int(x / 2)):
        pen.pendown()
        pen.pencolor(colors[int(y % sides)])
        pen.circle(x/5)
        pen.right(360 / sides -2)
        pen.penup()

    pen.setx(position[0])
    pen.sety(position[1])
    pen.setheading(heading)
    # pen.color(colors[x % len(colors)])
    pen.left(360 / sides + 2)
```

#### (3)、示例3

![](https://s2.ax1x.com/2019/02/11/kaDEvt.png)

```python
import turtle

pen = turtle.Pen()
# pen.penup()
turtle.bgcolor('black')
pen.speed(0)

colors = ['red', 'green', 'blue', 'yellow', 'white', 'purple', 'orange']
sides = int(turtle.numinput("提示", "请输入边数", 4, 1, 8))

for x in range(100):
    pen.forward(x * 4)
    # 记录位置，包含 x,y 坐标
    position = pen.position()
    # 记录角度
    heading = pen.heading()

    for y in range(sides):
        pen.pendown()
        pen.pencolor(colors[int(y % sides)])
        pen.circle(x/5)
        pen.right(360 / sides -2)
        pen.penup()

    pen.setx(position[0])
    pen.sety(position[1])
    pen.setheading(heading)
    pen.left(360 / sides + 2)
```
---

## 五、条件

### 1、if语句


![](https://s2.ax1x.com/2019/02/11/karNQI.png)

```python
import turtle

flag = turtle.textinput("提示","是否要运行程序：y/n:")

if flag == "y":

    pen = turtle.Pen()
    for x in range(100):
        pen.forward(x)
        pen.left(88)
    print("绘制完成")
```

### 2、布尔值

True、False 首字母必须大写


### 3、else 语句

#### (1)、示例1

```python
import turtle

flag = turtle.textinput("提示","是否要运行程序：y/n:")

if flag == "y":

    pen = turtle.Pen()
    for x in range(100):
        pen.forward(x)
        pen.left(88)
    print("绘制完成")
else:
    print("不需要执行程序")
```

#### (2)、示例2

![](https://s2.ax1x.com/2019/02/11/kayZK1.png)
 ...... ![](https://s2.ax1x.com/2019/02/11/kayvJe.png)

```python
import turtle

sides = int(turtle.numinput("提示", "输入边数：2-8：", 4))
pen = turtle.Pen()
pen.speed(10)
for x in range(200):
    pen.forward(x * 3)

    position = pen.position()
    heading = pen.heading()

    for y in range(sides):
        pen.pendown()
        if x % 2 == 0:
            pen.forward(x)
        else:
            pen.circle(x/4)

        pen.left(360 / sides + 2)
        pen.penup()

    pen.setx(position[0])
    pen.sety(position[1])
    pen.setheading(heading)
    pen.left(360 / sides - 2)
```


### 4、elif语句

等同于其他语言中的 else if .

格式为：

```
if 判断条件：
	语句1
elif 判断条件:
	语句2
else:
	语句3
``` 

### 5、if 语句中的 and、or、not

```python
num = eval(input("请输入一个数字，输入0退出程序："))

while num != 0:
    # 此处可以简化为 0 < num <= 10
    if 0 < num and num <= 10:
        print("您输入了一个不大于10的数值")

    elif 11 == num or 12 == num:
        print("您输入的是11或者12中的一个")

    elif not num > 20:
        print("您输入了一个不大于20的数值")
    else:
        print("您输入了一个大于20的值")

    num = eval(input("请输入一个数字，输入0退出程序："))
```

运行结果：

```
请输入一个数字，输入0退出程序：5
您输入了一个不大于10的数值
请输入一个数字，输入0退出程序：11
您输入的是11或者12中的一个
请输入一个数字，输入0退出程序：16
您输入了一个不大于20的数值
请输入一个数字，输入0退出程序：22
您输入了一个大于20的值
请输入一个数字，输入0退出程序：0

Process finished with exit code 0
```
### 6、秘密消息

凯撒密码，将字母向后移动 X 位之后获取新的字母。假设我们移动 13 位，那么就会得到下图的对应关系：

![kaWerd.png](https://s2.ax1x.com/2019/02/11/kaWerd.png)

#### (1)、字符串的操作函数

```python
inputStr = input("请输入字符串:")

# 大写
upperStr = inputStr.upper()
isUpper = upperStr.isupper()

# 小写
lowerStr = inputStr.lower()
isLower = lowerStr.islower()

# 字符串连接
mixtureStr = lowerStr + upperStr

print(inputStr)
print(upperStr, isUpper)
print(lowerStr, isLower)
print(mixtureStr)
```

运行结果：

```
请输入字符串:Hello
Hello
HELLO True
hello True
helloHELLO

Process finished with exit code 0
```
#### (2)、字符的ASCII码值

* `ord(字符)` 获取指定字符的 ASCII 码值
* `chr(数值)` 获取指定数值对应的字符 

```python
inputStr = input("请输入单个字符:")

ascNum = ord(inputStr)

ascChar = chr(ascNum)

print(inputStr, "对应的ASCII码值为：", ascNum)
print(ascNum, "对应的字符值为:", ascChar)
```

#### (3)、凯撒密码程序

```python
inputStr = input("请输入需要加密的字符串:")

upperStr = inputStr.upper()
secretStr = ""

for letter in upperStr:

    # 如果还不是大写，则表示非字符数据，可能是数值或者汉字等
    if letter.isupper():
        # 后移13位进行加密
        ascNum = ord(letter) + 13
        letter = chr(ascNum)

        # 如果加密之后变成了非大写，则在从A开始计算
        if not letter.isupper():
            letter = chr(ascNum - 26)

    secretStr += letter

print("加密前的内容为：", inputStr)
print("加密后的内容为：", secretStr)

decodeStr = ""
for char in secretStr:
    if char.isupper():
        ascNum2 = ord(char) - 13
        char = chr(ascNum2)

        if not char.isupper():
            char = chr(ascNum2 + 26)

    decodeStr += char
print("解密后的内容为：", decodeStr)
```

运行结果：

```
请输入需要加密的字符串:python4child
加密前的内容为： python4child
加密后的内容为： CLGUBA4PUVYQ
解密后的内容为： PYTHON4CHILD

Process finished with exit code 0
```


---
## 六、随机

需要导入 random 包，`import random`

### 1、猜数字游戏

* `random.randint(a,b)` 随机获取 a-b 之间的int值 

```python
import random

randomNum = random.randint(1, 10)
print("随机数值为", randomNum)

guessNum = int(input("请输入猜测的数值:"))

while guessNum != randomNum:
    if guessNum < randomNum:
        print("输入的数值小了")
    elif guessNum > randomNum:
        print("输入的数值大了")
    guessNum = int(input("请输入猜测的数值:"))

print("厉害，猜对了")
```

运行结果：

```
随机数值为 5
请输入猜测的数值:1
输入的数值小了
请输入猜测的数值:6
输入的数值大了
请输入猜测的数值:5
厉害，猜对了

Process finished with exit code 0
```

### 2、彩色的随机螺旋线

* `random.choice(列表或集合)` 随机获取指定列表或集合中的元素
* turtle 对象的 `setPos(x,y)` 表示为画笔设置位置
* `turtle.window_width()`、`turtle.window_height()` 分别用来获取画布的宽度和高度
* turtle 库 中画布的中心点坐标为 (0,0), 与 Android 等中的不同。
* `random.randrange（a,b）` 获取 a-b 之间的数据

#### (1)、画布中随机的位置画随机大小的圆

![](https://s2.ax1x.com/2019/02/11/kaIO8x.png)

```python
import random
import turtle

colors = ['red', 'green', 'blue', 'orange', 'black']

pen = turtle.Pen()
pen.speed(10)

# 获取画布的大小
turtleWidth = turtle.window_width()
turtleHeight = turtle.window_height()

for x in range(50):
    pen.penup()
    
    # 保证随机的坐标点不要超过屏幕，取负值是为了能够在中心点左侧绘制——中心点坐标为(0,0)
    randX = random.randrange(-turtleWidth // 2, turtleWidth // 2)
    randY = random.randrange(-turtleHeight // 2, turtleHeight // 2)

	# 设置画笔的位置
    pen.setpos(randX, randY)

    randSize = random.randint(20, 50)
    randColor = random.choice(colors)
    pen.color(randColor)
    pen.pendown()

    for y in range(randSize):
        pen.forward(y)
        pen.left(92)
```


### 3、Scissors-Rock-Paper（剪刀，石头，布）

模拟计算机和用户玩剪刀石头布的游戏。

```python
import random

choices = ["剪刀", "石头", "布"]

inputStr = input("请输入，或输入 q 退出游戏：")
print("你出的是：", inputStr)

while inputStr != "q":
    # 计算机随机一个选项
    randChoice = random.choice(choices)
    print("对方出的是：", randChoice)

    if randChoice == inputStr:
        print("平局")

    elif inputStr == "剪刀":
        if randChoice == "石头":
            print("你输了")
        else:
            print("你赢了")

    elif inputStr == "石头":
        if randChoice == "布":
            print("你输了")
        else:
            print("你赢了")

    elif inputStr == "布":
        if randChoice == "剪刀":
            print("你输了")
        else:
            print("你赢了")
    else:
        print("输入不合法")

    inputStr = input("请输入，或输入 q 退出游戏：")
    print("你出的是：", inputStr)

print("游戏结束")
```

运行结果：

```
请输入，或输入 q 退出游戏：剪刀
你出的是： 剪刀
对方出的是： 剪刀
平局
请输入，或输入 q 退出游戏：石头
你出的是： 石头
对方出的是： 剪刀
你赢了
请输入，或输入 q 退出游戏：布
你出的是： 布
对方出的是： 石头
你赢了
请输入，或输入 q 退出游戏：q
你出的是： q
游戏结束

Process finished with exit code 0
```


### 4、任选一张牌(比较牌面大小)

* `a.index(b)` a 为数组或列表，b 为其中的元素，这个表达式的含义是获取 b 在 a 中的索引位置

```python
import random

faces = ["红桃", "黑桃️", "梅花️", "方块"]
nums = [2, 3, 4, 5, 6, 7, 8, 9, 10, "Jack", "Queen", "King", "ACE"]

flag = input("继续运行或输入q终止程序：")

while flag != "q":
    randFace1 = random.choice(faces)
    ranNum1 = random.choice(nums)
    print("你的牌面为:", randFace1, ranNum1)

    randFace2 = random.choice(faces)
    ranNum2 = random.choice(nums)
    print("Ta的牌面为：", randFace2, ranNum2)

    if nums.index(ranNum1) > nums.index(ranNum2):
        print("你赢了")
    elif nums.index(ranNum1) > nums.index(ranNum2):
        print("平局")
    else:
        print("你输了")

    flag = input("继续运行或输入q终止程序：")

print("游戏结束")
```

运行结果：

```
继续运行或输入q终止程序：
你的牌面为: 梅花️ 10
Ta的牌面为： 方块 Jack
你输了
继续运行或输入q终止程序：
你的牌面为: 方块 7
Ta的牌面为： 梅花️ 3
你赢了
继续运行或输入q终止程序：q
游戏结束

Process finished with exit code 0
```

### 5、掷骰子

5个 🎲 一起掷，判断有几个相同的点数.

* `a[x]=b` a 为列表或数组，x 为索引，b 为值，该示例含义为：为列表的x索引赋值为b
* `b=a[x]` 取出列表 a 的 x 索引值
* `a.sort()` a 为列表或数组，该示例含义为 升序排列 a 中的元素 

```python
import random

# 骰子有6个面
choices = [1, 2, 3, 4, 5, 6]

# 预置有5个元素的列表
results = [0, 0, 0, 0, 0]

flag = input("敲击回车继续或q退出：")

while flag != "q":

    for x in range(len(results)):
        results[x] = random.choice(choices)

    # 对结果进行排序
    results.sort()
    print("排序后的结果为：", results)

    # 根据索引获取值
    if results[0] == results[4]:
        print("排序后，第一个和第五个一致，则全部一致")
    elif results[0] == results[3] or results[1] == results[4]:
        print("排序后，第一个等于第四个 或 第二个等于最后一个，则有四个相等")
    elif results[0] == results[2] or results[1] == results[3] or results[2] == results[4]:
        print("排序后，第一个等于第三个 或 第二个等于第四个 或第三个等于第五个，则有三个相等")
    else:
        print("相等的数量不足三个")
    flag = input("敲击回车继续或q退出：")
print("游戏结束")
``` 

运行结果：

```
敲击回车继续或q退出：
排序后的结果为： [2, 5, 6, 6, 6]
排序后，第一个等于第三个 或 第二个等于第四个 或第三个等于第五个，则有三个相等
敲击回车继续或q退出：
排序后的结果为： [1, 2, 3, 4, 6]
相等的数量不足三个
敲击回车继续或q退出：q
游戏结束

Process finished with exit code 0
```

### 6、镜像万花筒

由于 turtle画布中心点的坐标为（0，0），那么，围绕该原点，每一个坐标点（x,y）都会有三个相应的镜像点，分别为：(-x,-y),(-x,y),(x,-y)

![](https://s2.ax1x.com/2019/02/11/kaLhFg.png)

```python
import random
import turtle

colors = ['red', 'green', 'blue', 'orange']

turtleWidth = turtle.window_width()
turtleHeight = turtle.window_height()

pen = turtle.Pen()

sides = int(turtle.numinput("提示", "请输入边的数量", 4))

for x in range(20):
    pen.pencolor(random.choice(colors))
    randSize = random.randint(20, 50)
    randX = random.randrange(-turtleWidth // 2, turtleWidth // 2)
    randY = random.randrange(-turtleHeight // 2, turtleHeight // 2)

    pen.penup()
    pen.setpos(randX, randY)
    pen.pendown()

    for y in range(randSize):
        pen.forward(y * 2)
        pen.left(360 / sides + 2)

    # 镜像1
    pen.penup()
    pen.setpos(-randX, randY)
    pen.pendown()

    for y in range(randSize):
        pen.forward(y * 2)
        pen.left(360 / sides + 2)

    # 镜像2
    pen.penup()
    pen.setpos(randX, -randY)
    pen.pendown()

    for y in range(randSize):
        pen.forward(y * 2)
        pen.left(360 / sides + 2)

    # 镜像三
    pen.penup()
    pen.setpos(-randX, -randY)
    pen.pendown()

    for y in range(randSize):
        pen.forward(y * 2)
        pen.left(360 / sides + 2)
```

---

## 七、函数

### 1、用函数整合内容

定义空参函数的基本格式：

```
def 函数名():
	函数体
```

### 2、参数

定义需要传递参数的函数的格式：

```
def 函数名(参数1，参数2):
	函数体
```

下面的示例将逐步演示如何绘制一个笑脸 😁

#### (1)、绘制黄色圆

```python
import turtle

turtle.bgcolor('black')
pen = turtle.Pen()

pen.pencolor('yellow')
pen.fillcolor('yellow')
pen.begin_fill()
pen.circle(50)
pen.end_fill()
```
#### (2)、绘制眼睛

* 黄色笑脸的左上方为左眼，其坐标相对于圆脸的原点来说，x 变小，y 变大

```python
import random
import turtle

turtle.bgcolor('black')
pen = turtle.Pen()

turtleWidth = turtle.window_width()
turtleHeight = turtle.window_height()

randX = random.randrange(-turtleWidth // 2, turtleWidth // 2)
randY = random.randrange(-turtleHeight // 2, turtleHeight // 2)

# 设置圆最底端的点
pen.setpos(randX, randY)
pen.pencolor('yellow')
pen.fillcolor('yellow')
pen.begin_fill()
# 设置圆的半径
pen.circle(50)
pen.end_fill()

# 绘制左眼
pen.setpos(randX - 15, randY + 50)
pen.pencolor('blue')
pen.fillcolor('blue')
pen.begin_fill()
pen.circle(10)
pen.end_fill()
pen.penup()

# 绘制右眼
pen.setpos(randX + 15, randY + 50)
pen.pencolor('blue')
pen.fillcolor('blue')
pen.pendown()
pen.begin_fill()
pen.circle(10)
pen.end_fill()
pen.penup()
```
#### (3)、绘制嘴巴

```python
 # 绘制嘴巴
    pen.penup()
    pen.setpos(randX - 15, randY + 25)
    pen.pencolor('black')
    pen.width(8)
    pen.pendown()
    # 将画笔拖拽到指定位置（会画出拖拽路径）
    pen.goto(randX - 10, randY + 20)
    pen.goto(randX - 5, randY + 17)
    pen.goto(randX + 5, randY + 17)
    pen.goto(randX + 10, randY + 20)
    pen.goto(randX + 15, randY + 25)
```
#### (4)、完整示例

![](https://s2.ax1x.com/2019/02/11/kaxfRe.png)

```python
import random
import turtle


# 定义绘制笑脸的函数
def draw_smile_face(randX, ranxY, pen):
    pen.penup()
    # 设置圆的起始绘制点，也就是圆的最底端的点
    pen.setpos(randX, randY)
    pen.pencolor('yellow')
    pen.fillcolor('yellow')
    pen.pendown()
    pen.width(1)
    pen.begin_fill()
    # 设置圆的半径
    pen.circle(50)
    pen.end_fill()

    # 绘制左眼
    pen.setpos(randX - 15, randY + 50)
    pen.pencolor('blue')
    pen.fillcolor('blue')
    pen.begin_fill()
    pen.circle(10)
    pen.end_fill()

    # 绘制右眼
    pen.penup()
    pen.setpos(randX + 15, randY + 50)
    pen.pencolor('blue')
    pen.fillcolor('blue')
    pen.pendown()
    pen.begin_fill()
    pen.circle(10)
    pen.end_fill()

    # 绘制嘴巴
    pen.penup()
    pen.setpos(randX - 15, randY + 25)
    pen.pencolor('black')
    pen.width(8)
    pen.pendown()
    # 将画笔拖拽到指定位置（会画出拖拽路径）
    pen.goto(randX - 10, randY + 20)
    pen.goto(randX - 5, randY + 17)
    pen.goto(randX + 5, randY + 17)
    pen.goto(randX + 10, randY + 20)
    pen.goto(randX + 15, randY + 25)


# 调用绘制笑脸的函数
turtle.bgcolor('black')
pen = turtle.Pen()

turtleWidth = turtle.window_width()
turtleHeight = turtle.window_height()

for x in range(100):
    randX = random.randrange(-turtleWidth // 2, turtleWidth // 2)
    randY = random.randrange(-turtleHeight // 2, turtleHeight // 2)
    draw_smile_face(randX, randY, pen)
```


### 3、返回值

#### (1)、在函数中返回数据

* 直接在函数体中使用return语句将数据返回即可，

```python
def convert_in2cm(inches):
    # 将英尺转换为厘米. 1英尺=2.54厘米
    return inches * 2.54

def convert_lp2kg(pounds):
    # 将镑转换为千克。1千克=2.2镑
    return pounds / 2.2

print("2英尺转换为厘米后为：", convert_in2cm(2))
print("3镑转换为千克为：", convert_lp2kg(3))
```

运行结果：

```
2英尺转换为厘米后为： 5.08
3镑转换为千克为： 6.6000000000000005

Process finished with exit code 0
```

#### (2)、使用函数的返回值

How many Ping-Pong balls tall am I? (我有多少个乒乓球那么高？)
What is my weight in Ping-Pong balls? (我又多少个乒乓球那么重？)

一个正规乒乓球的重量为 2.7克（0.095盎司），高度为 40 毫米（4厘米或者1.57英寸）。
美国人通常使用镑表示体重，使用英尺和英寸表示身高。1英尺=12英寸

```python
def convert_in2cm(inches):
    # 将英尺转换为厘米
    return inches * 2.54


def convert_lp2kg(pounds):
    # 将镑转换为千克
    return pounds / 2.2


weight_in = int(input("请输入您的体重，单位镑："))
height_in = int(input("请输入您的身高，单位英寸："))

# 将英寸转换为厘米然后除以单个乒乓球的高度——4厘米
ping_pong_tall = round(convert_in2cm(height_in) / 4)
# 将镑转换为克，然后除以单个乒乓球的重量——2.7克
ping_pong_heavy = round(convert_lp2kg(weight_in) * 1000 / 2.7)

# 计算身高为几英尺几英寸
feet = height_in // 12
inches = height_in % 12

print("您的身高和体重为：", feet, "英尺", inches, "英寸。")
print("相当于", ping_pong_tall, "get乒乓球的高度和", ping_pong_heavy, "个乒乓球的重量")
```

运行结果：

```
请输入您的体重，单位镑：138
请输入您的身高，单位英寸：70
您的身高和体重为： 5 英尺 10 英寸。
相当于 44 get乒乓球的高度和 23232 个乒乓球的重量

Process finished with exit code 0
```

### 4、交互简介

注意：该小节内容并不能像书中描述一样显示交互界面等到用户交互，而是运行完毕之后，画布直接关闭了。后面的第五小节也是这样子。

#### (1)、处理事件

* `turtle.onscreenclick(函数名)` 这种接收一个函数作为参数的函数被称为回调函数。参数中的函数不需要小括号和参数。

```python
import turtle

pen = turtle.Pen()
turtle.onscreenclick(pen.setpos)
```

通错上面的代码就创建了一个简单的绘图板，就可以在绘图板上绘制自己需要的内容了。

#### (2)、监听事件

监听键盘上的方向键，通过方向键的移动实现绘制事件

* `turtle.onkeypress(回调函数名，被监听的按键)` 可以监听键盘上按键被点击的事件
* `turtle.listen()` 告知键盘开始启动监听

```python
import turtle

pen = turtle.Pen()

# 自定义上键触发时的事件
def on_up():
    pen.forward(50)


def on_left():
    pen.left(92)


def on_right():
    pen.right(92)

# 监听键盘事件，第一个参数为函数名，第二个参数为键盘名称
turtle.onkeypress(on_up, "Up")
turtle.onkeypress(on_left, "Left")
turtle.onkeypress(on_right, "Right")

# 启动监听
turtle.listen()
```

#### (3)、用参数处理事件

在用户点击的位置绘制螺旋线。

```python
import random
import turtle

pen = turtle.Pen()

colors = ['red', 'green', 'blue', 'yellow', 'orange', 'purple', 'white']
turtle.bgcolor('black')


def random_draw(x, y):
    pen.color(random.choice(colors))
    pen.setpos(x, y)
    pen.pendown()
    for x in range(random.randint(10, 40)):
        pen.forward(x * 2)
        pen.left(92)
    pen.penup()


# 此处 random_draw 函数不需要传递参数，
# 触发点击事件之后，会自动把 x,y 坐标反馈给 random_draw 函数
turtle.onscreenclick(random_draw)
```

#### (4)、更进一步

内容省略。

该小节讲述了使用点击回调让用户在点击的位置绘制笑脸。

```python
#ClickAndSmile.py
import random
import turtle
t = turtle.Pen()
t.speed(0)
t.hideturtle()
turtle.bgcolor("black")
def draw_smiley(x,y):
    t.penup()
    t.setpos(x,y)
    t.pendown()
    # Face
    t.pencolor("yellow")
    t.fillcolor("yellow")
    t.begin_fill()
    t.circle(50)
    t.end_fill()
    # Left eye
    t.setpos(x-15, y+60)
    t.fillcolor("blue")
    t.begin_fill()
    t.circle(10)
    t.end_fill()
    # Right eye
    t.setpos(x+15, y+60)
    t.begin_fill()
    t.circle(10)
    t.end_fill()
    # Mouth
    t.setpos(x-25, y+40)
    t.pencolor("black")
    t.width(10)
    t.goto(x-10, y+20)
    t.goto(x+10, y+20)
    t.goto(x+25, y+40)
    t.width(1)
turtle.onscreenclick(draw_smiley)
```

### 5、在制定的位置绘制镜像万花筒

```python
#ClickKaleidoscope.py
import random
import turtle
t = turtle.Pen()
t.speed(0)
t.hideturtle()
turtle.bgcolor("black")
colors = ["red", "yellow", "blue", "green", "orange", "purple",
          "white", "gray"]
def draw_kaleido(x,y):
    t.pencolor(random.choice(colors))
    size = random.randint(10,40)
    draw_spiral(x,y, size)
    draw_spiral(-x,y, size)
    draw_spiral(-x,-y, size)
    draw_spiral(x,-y, size)
def draw_spiral(x,y, size):
    t.penup()
    t.setpos(x,y)
    t.pendown()
    for m in range(size):
        t.forward(m*2)
        t.left(92)
turtle.onscreenclick(draw_kaleido)
```

---

## 八、定时器和动画

Turtle库比较慢，无法适用于大量的动画或者移动对象，所以，需要使用 Pygame库 替代。


安装Pygame时，需要先更新python到最新版本，然后让项目关联到最新版

[最新版python下载路径](https://www.python.org/downloads/mac-osx/)

* 让项目关联到最新版python

![](https://s2.ax1x.com/2019/02/12/kdNbJ1.png)

![](https://s2.ax1x.com/2019/02/12/kdNfMT.png)

* 在项目中导入 Pygame ，然后使用自动安装

![](https://s2.ax1x.com/2019/02/12/kdt2uD.png)

[或者也可以点击此处直接参考 Pygame 官网的安装方式](https://www.pygame.org/wiki/GettingStarted#Mac%20installation). 这种方式可能会使用到pip, 其安装命令为：`sudo easy_install pip`

### 1、获取Pygame的所有GUI
#### (1)、Pygame画一个点

* Pygame 中画布左上角坐标为 （0，0）

![](https://s2.ax1x.com/2019/02/12/kddGa4.png)

```python
import pygame

pygame.init()

keep_going = True
# 定义画布尺寸
screen = pygame.display.set_mode([800, 600])
# 定义RGB格式的颜色值
color = (0, 255, 0)
# 定义绘制半径
radius = 50

while keep_going:
    # 获取 pygame 的事件类型，并判断是否为QUIT
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
    # 画圆，参数依次为：画布，颜色，圆心坐标，半径
    pygame.draw.circle(screen, color, (100, 100), radius)
    # 更新画布内容
    pygame.display.update()
# 绘制完毕退出程序
pygame.quit()
```
#### (2)、Pygame中的新内容

* 画布左上角为原点（0，0）
* `pygame.event.get()` 用来获取交互事件类型
* 在获取事件类型的时候需要使用循环进行获取和判断

 
#### (3)、游戏的部分

使用 pygame 绘制本地笑脸图片到画布。

![](https://s2.ax1x.com/2019/02/12/kd0y8A.png)

```python
import pygame

pygame.init()
# 加载图片，将图片放置在当前文件同一目录下
pic = pygame.image.load("CrazySmile.bmp")
# 创建画布
screen = pygame.display.set_mode([800, 600])
keep_going = True

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
    # 将图片显示在画布上。第二个参数坐标表示图片左上角的坐标位置
    screen.blit(pic, (100, 100))
    pygame.display.update()

pygame.quit()
```

### 2、移动和弹跳

#### (1)、移动笑脸

从左上角开始慢慢移动到右下角，直接移出画布

![](https://s2.ax1x.com/2019/02/12/kdr72d.png)

```python
import pygame

pygame.init()

screen = pygame.display.set_mode([600, 600])
pic = pygame.image.load("CrazySmile.bmp")

keep_going = True
picX = 0
picY = 0

# 设置 colorkey 的作用是去除头像四个角上的黑边
colorkey = pic.get_at((0, 0))
pic.set_colorkey(colorkey)

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False

    # 去除每次移动时的轨迹（或者说是拖影）。
    # 在 Python 3.7.2 + ygame 1.9.4 环境下没看到拖影
    BLACK = (0, 0, 0)
    screen.fill(BLACK)

    # 每一次触发循环坐标点+1
    picX += 1
    picY += 1
    screen.blit(pic, (picX, picY))
    pygame.display.update()

pygame.quit()
```
#### (2)、用Clock类控制笑脸动画

使用 Clock 类控制动画执行的速度。

* `timer = pygame.time.Clock()` 获取时钟对象
* `timer.tick(60)` 控制每秒绘制的次数为60次


```python

import pygame

pygame.init()

screen = pygame.display.set_mode([600, 600])
pic = pygame.image.load("CrazySmile.bmp")

keep_going = True
picX = 0
picY = 0

# 设置 colorkey 的作用是去除头像四个角上的黑边
colorkey = pic.get_at((0, 0))
pic.set_colorkey(colorkey)

# 获取时钟对象
timer = pygame.time.Clock()

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False

    # 去除每次移动时的轨迹（或者说是拖影）。
    BLACK = (0, 0, 0)
    screen.fill(BLACK)

    # 每一次触发循环坐标点+1
    picX += 1
    picY += 1
    screen.blit(pic, (picX, picY))
    pygame.display.update()

    # 限制每秒只绘制的帧数，达到控制动画速率的效果。推荐60帧
    timer.tick(10)

pygame.quit()
```

#### (3)、将笑脸从墙上弹开

* `screen.blit(pic,(x,y))` 中传递的坐标参数实际是图片左上角的坐标
* `pic.get_width()`、`pic.get_height()` 可以分别获取图片的宽度和高度。

运行下列程序之后，会看到从 左上--右下--左上 无限循环的动画效果

```python
import pygame

pygame.init()

screen = pygame.display.set_mode([600, 600])
pic = pygame.image.load("CrazySmile.bmp")

keep_going = True
picX = 0
picY = 0
speed = 5

# 获取图片的宽高
pic_width = pic.get_width()
pic_height = pic.get_height()

# 设置 colorkey 的作用是去除头像四个角上的黑边
colorkey = pic.get_at((0, 0))
pic.set_colorkey(colorkey)

# 获取时钟对象
timer = pygame.time.Clock()

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False

    # 去除每次移动时的轨迹（或者说是拖影）。
    BLACK = (0, 0, 0)
    screen.fill(BLACK)

    # 每一次触发循环该表坐标点都会变化
    picX += speed
    picY += speed

    # 如果移动到了左边界或者右边界。前面定义了画布总宽600
    if picX <= 0 or picX + pic_width >= 600:
        speed = -speed

    screen.blit(pic, (picX, picY))
    pygame.display.update()

    # 限制每秒只绘制的帧数，达到控制动画速率的效果。推荐60帧
    timer.tick(60)

pygame.quit()
```


#### (4)、在四面墙上弹回笑脸

```python
import pygame

pygame.init()

screen_width = 800
screen_height = 600
screen = pygame.display.set_mode([screen_width, screen_height])
pic = pygame.image.load("CrazySmile.bmp")

keep_going = True
picX = 0
picY = 0

# 每帧移动的像素数量
speedx = 5
speedy = 5

# 获取图片的宽高
pic_width = pic.get_width()
pic_height = pic.get_height()

# 设置 colorkey 的作用是去除头像四个角上的黑边
colorkey = pic.get_at((0, 0))
pic.set_colorkey(colorkey)

# 获取时钟对象
timer = pygame.time.Clock()

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False

    # 去除每次移动时的轨迹（或者说是拖影）。
    BLACK = (0, 0, 0)
    screen.fill(BLACK)

    # 每一次触发循环该表坐标点
    picX += speedx
    picY += speedy

    # 如果移动到了左边界或者右边界。前面定义了画布总宽600
    if picX <= 0 or picX + pic_width >= screen_width:
        speedx = -speedx

    if picY <= 0 or picY + pic_height >= screen_height:
        speedy = -speedy

    screen.blit(pic, (picX, picY))
    pygame.display.update()

    # 限制每秒只绘制的帧数，达到控制动画速率的效果。推荐60帧
    timer.tick(60)

pygame.quit()
```

如果去掉上述代码中的 `screen.fill(BLACK)` 就会看到下图中的移动轨迹：

![](https://s2.ax1x.com/2019/02/12/kdcQMj.png)

### 3、编程挑战——练习

#### (1)、颜色变化的点

颜色随机的点。

```python
import random
import pygame

pygame.init()

color = (random.randint(0, 255), random.randint(0, 255), random.randint(0, 255))
screen = pygame.display.set_mode((800, 600))

keep_going = True

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
    pygame.draw.circle(screen, color, (50, 100), 30)
    pygame.display.update()

pygame.quit()
```

#### (2)、100个颜色、位置均随机的点

![](https://s2.ax1x.com/2019/02/12/kd7OpR.png)

```python
import random

import pygame

pygame.init()
screen = pygame.display.set_mode((800, 600))
keep_going = True

# 定义三个容量为100的列表，用来存储颜色、位置、大小
colors = [0] * 100
positions = [0] * 100
sizes = [0] * 100

# 初始化三个列表
for x in range(100):
    colors[x] = (random.randint(0, 255), random.randint(0, 255), random.randint(0, 255))
    positions[x] = (random.randint(0, 800), random.randint(0, 600))
    sizes[x] = random.randint(10, 100)

while keep_going:
    # 监听事件
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False

    # 执行绘制
    for n in range(100):
        pygame.draw.circle(screen, colors[n], positions[n], sizes[n])
        pygame.display.update()

pygame.quit()
```

#### (3)、雨点

运行下列程序之后，得到的是一片从左上角向右下角移动的彩色小点。

```python
import random

import pygame

pygame.init()
screen = pygame.display.set_mode([800, 600])
keep_going = True

# 定义三个容量为100的列表，用来存储颜色、位置、大小
colors = [0] * 100
positions = [0] * 100
sizes = [0] * 100

# 初始化三个列表
for x in range(100):
    colors[x] = (random.randint(0, 255), random.randint(0, 255), random.randint(0, 255))
    positions[x] = (random.randint(0, 800), random.randint(0, 600))
    sizes[x] = random.randint(10, 60)

BLACK = (0, 0, 0)

while keep_going:
    # 监听事件
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False

    # 执行绘制
    for n in range(100):
        pygame.draw.circle(screen, colors[n], positions[n], sizes[n])

        # positions 列表中存储的实际是一个元组，元组第一个元素为x坐标，第二个元素为y坐标
        new_x = positions[n][0] + 1
        new_y = positions[n][1] + 1

        # 超出屏幕之后再回到左上角位置
        if new_x > 800:
            new_x -= 800
        if new_y > 600:
            new_y -= 600
        positions[n] = (new_x, new_y)

    # 更新界面是在for循环外部，while循环内部的
    pygame.display.update()
    # 去除拖影的操作需要放在update之后
    screen.fill(BLACK)

pygame.quit()
```

![](https://s2.ax1x.com/2019/02/12/kdbczn.png)

![](https://s2.ax1x.com/2019/02/12/kdbRs0.png)

---

## 九、用户交互——进入游戏

### 1、增加交互：点击和拖动

#### (1)、点击绘制

```python
import pygame

pygame.init()
screen = pygame.display.set_mode([800, 600])

color = (255, 0, 0)
keep_going = True

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False

        elif pygame.MOUSEBUTTONDOWN == event.type:
            # 监听鼠标点击事件，并获取点击的位置
            position = event.pos
            pygame.draw.circle(screen, color, position, 15)
    pygame.display.update()
pygame.quit()
```

运行上述程序后，在画布中点击鼠标左键就可以开始绘制了：

下图是以点击的形式绘制的一个小房子

![](https://s2.ax1x.com/2019/02/12/kdLRET.png)

#### (2)、拖拽绘制

```python
import pygame
pygame.init()

# 设置绘图板的标题
pygame.display.set_caption("拖拽鼠标开始绘制")

color = (0, 255, 0)
keep_going = True
is_mouse_down = False

screen = pygame.display.set_mode([800, 600])

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
        elif pygame.MOUSEBUTTONDOWN == event.type:
            is_mouse_down = True
        elif pygame.MOUSEBUTTONUP == event.type:
            is_mouse_down = False
    if is_mouse_down:
        # 获取拖拽的每一个位置点
        position = pygame.mouse.get_pos()
        pygame.draw.circle(screen, color, position, 10)
    pygame.display.update()
pygame.quit()
```

![](https://s2.ax1x.com/2019/02/12/kdOqQs.png)


### 2、高级交互：笑脸爆炸

#### (1)、精灵

##### A: 精灵 Sprite
Sprite 精灵，在屏幕上移动的图形化对象被称为精灵。

Pygame 通过 pygame.sprite.Sprite 类提供了对精灵图形的支持。

另外，Pygame 提供了一个 Group 类，这是一个容器类，用来存储一组 Sprite 对象，并提供了增删和更新 Sprite 对象的方法。

`sprite_list=pygame.sprite.Group()` 表示获取 Group 对象。


##### B、自定义继承自 Sprite 的类

注意这里的继承方式。

```python
import pygame

class Smiley(pygame.sprite.Sprite):
    # 笑脸的坐标元组
    pos = (0, 0)
    # vel 是 velocity 的简写，表示移动速度
    x_vel = 1
    y_vel = 1
    # 笑脸的缩放比率
    scale = 100
```

#### (2)、设置精灵——构造函数

* `_init_(self)` 是构造函数的基本格式

```python
import pygame

pygame.init()
screen = pygame.display.set_mode([800, 600])
pygame.display.set_caption("笑脸😁")

is_mouse_down = False
keep_going = True
clock = pygame.time.Clock()
# 图片放在当前文件所在的同一目录下
pic = pygame.image.load("CrazySmile.bmp")
# 定义group对象，用来存储一组sprite
sprite_list = pygame.sprite.Group()


class Smiley(pygame.sprite.Sprite):
    # 笑脸的坐标元组
    pos = (0, 0)
    # vel 是 velocity 的简写，表示移动速度
    x_vel = 1
    y_vel = 1
    # 笑脸的收缩比率
    scale = 100

    # 定义构造函数
    def __init__(self, pos, x_vel, y_vel):
        pygame.sprite.Sprite.__init__(self)
        self.pos = pos
        self.x_vel = x_vel
        self.y_vel = y_vel

        self.image = pic
        self.rect = self.image.get_rect()
        self.rect.x = pos[0] - self.scale / 2
        self.rect.y = pos[1] - self.scale / 2
```


#### (3)、更新精灵

重写 pygame 的 update() 函数，在其中实现自己需要的操作。

```python
import pygame

pygame.init()
screen = pygame.display.set_mode([800, 600])
pygame.display.set_caption("笑脸😁")

is_mouse_down = False
keep_going = True
clock = pygame.time.Clock()
# 图片放在当前文件所在的同一目录下
pic = pygame.image.load("CrazySmile.bmp")
# 定义group对象，用来存储一组sprite
sprite_list = pygame.sprite.Group()


class Smiley(pygame.sprite.Sprite):
    # 笑脸的坐标元组
    pos = (0, 0)
    # vel 是 velocity 的简写，表示移动速度
    x_vel = 1
    y_vel = 1
    # 笑脸的收缩比率
    scale = 100

    # 定义构造函数
    def __init__(self, pos, x_vel, y_vel):
        pygame.sprite.Sprite.__init__(self)
        self.pos = pos
        self.x_vel = x_vel
        self.y_vel = y_vel

        self.image = pic
        self.rect = self.image.get_rect()
        self.rect.x = pos[0] - self.scale / 2
        self.rect.y = pos[1] - self.scale / 2

    # 重写update函数
    def update(self):
        self.rect.x += self.x_vel
        self.rect.y += self.y_vel
        # screen.get_width() 表示获取画布宽度
        if self.rect.x < 0 or self.rect.x > screen.get_width() - self.scale:
            self.x_vel = -self.x_vel
        if self.rect.y < 0 or self.rect.y > screen.get_height() - self.scale:
            self.y_vel = -self.y_vel
```


#### (4)、较大和较小的笑脸

* 获取缩放之后的图片：

```python
self.image = pygame.transform.scale(self.image, (self.scale, self.scale))
```



#### (5)、完整代码

* 绘制 Group 对象中的精灵对象:`sprite_list.draw(screen)`
* 更新 Group 对象中的精灵对象: `sprite_list.update()`
* 添加精灵对象到 Group 中：`sprite_list.add(xx)`

运行下列程序后，点击鼠标左键就会在屏幕上创建多个会滚动的大小不一的笑脸。

```python
import random
import pygame

pygame.init()
screen = pygame.display.set_mode([800, 600])
pygame.display.set_caption("笑脸😁")

is_mouse_down = False
keep_going = True
clock = pygame.time.Clock()
# 图片放在当前文件所在的同一目录下
pic = pygame.image.load("CrazySmile.bmp")
# 定义group对象，用来存储一组sprite
sprite_list = pygame.sprite.Group()


class Smiley(pygame.sprite.Sprite):
    # 笑脸的坐标元组
    pos = (0, 0)
    # vel 是 velocity 的简写，表示每帧位移的像素数
    x_vel = 1
    y_vel = 1
    # 笑脸的收缩比率
    scale = 100

    # 定义构造函数
    def __init__(self, pos, x_vel, y_vel):
        pygame.sprite.Sprite.__init__(self)
        self.pos = pos
        self.x_vel = x_vel
        self.y_vel = y_vel

        self.image = pic

        self.scale = random.randint(10, 100)
        # 获取缩放之后的图片
        self.image = pygame.transform.scale(self.image, (self.scale, self.scale))

        self.rect = self.image.get_rect()
        self.rect.x = pos[0] - self.scale / 2
        self.rect.y = pos[1] - self.scale / 2

    # 重写update函数
    def update(self):
        self.rect.x += self.x_vel
        self.rect.y += self.y_vel
        # screen.get_width() 表示获取画布宽度
        if self.rect.x < 0 or self.rect.x > screen.get_width() - self.scale:
            self.x_vel = -self.x_vel
        if self.rect.y < 0 or self.rect.y > screen.get_height() - self.scale:
            self.y_vel = -self.y_vel


while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
        elif pygame.MOUSEBUTTONDOWN == event.type:
            is_mouse_down = True
        elif pygame.MOUSEBUTTONUP == event.type:
            is_mouse_down = False
    # 去除拖影
    screen.fill((0, 0, 0))
    # 绘制精灵对象
    sprite_list.draw(screen)
    sprite_list.update()
    # 每秒刷新60帧
    clock.tick(60)

    pygame.display.update()

    if is_mouse_down:
        pos = pygame.mouse.get_pos()
        speedx = random.randrange(-5, 5)
        speedy = random.randrange(-5, 5)

        # 创建精灵对象并添加到Group对象中
        scale_smiley = Smiley(pos, speedx, speedy)
        sprite_list.add(scale_smiley)
pygame.quit()
```

### 3、SmilePop 1.0版本
#### (1)、检测碰撞和删除精灵

* `pygame.sprite.collide_rect()`可以用来检测两个精灵所在的矩形区域是否产生了碰撞。
* 通过精灵对象所在矩形函数可以检测该精灵是否与某个指定的点有碰撞： `rect.collidepoint(point)`
* `pygame.mouse.get_pressed()[0]` 表示鼠标左键，[2] 表示右键
*  获取相交的点，并存储到列表中的代码如下：
```python
collideSprite = [s for s in sprite_list if s.rect.collidepoint(pos)]
```

运行下列程序之后，点击鼠标左键就会在画布中添加多个随机大小的笑脸，而点击鼠标右键则会删除被触摸到的笑脸。(注意：在 MAC Pro 中，根据触摸板功能设置的不同，可能会触发不同的事件)

```python
import random

import pygame

pygame.init()
screen = pygame.display.set_mode([800, 600])
pygame.display.set_caption("笑脸😁")

is_mouse_down = False
keep_going = True
clock = pygame.time.Clock()
# 图片放在当前文件所在的同一目录下
pic = pygame.image.load("CrazySmile.bmp")
# 定义group对象，用来存储一组sprite
sprite_list = pygame.sprite.Group()


class Smiley(pygame.sprite.Sprite):
    # 笑脸的坐标元组
    pos = (0, 0)
    # vel 是 velocity 的简写，表示每帧位移的像素数
    x_vel = 1
    y_vel = 1
    # 笑脸的收缩比率
    scale = 100

    # 定义构造函数
    def __init__(self, pos, x_vel, y_vel):
        pygame.sprite.Sprite.__init__(self)
        self.pos = pos
        self.x_vel = x_vel
        self.y_vel = y_vel

        self.image = pic

        self.scale = random.randint(10, 100)
        # 获取缩放之后的图片
        self.image = pygame.transform.scale(self.image, (self.scale, self.scale))

        self.rect = self.image.get_rect()
        self.rect.x = pos[0] - self.scale / 2
        self.rect.y = pos[1] - self.scale / 2

    # 重写update函数
    def update(self):
        self.rect.x += self.x_vel
        self.rect.y += self.y_vel
        # screen.get_width() 表示获取画布宽度
        if self.rect.x < 0 or self.rect.x > screen.get_width() - self.scale:
            self.x_vel = -self.x_vel
        if self.rect.y < 0 or self.rect.y > screen.get_height() - self.scale:
            self.y_vel = -self.y_vel


while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
        elif pygame.MOUSEBUTTONDOWN == event.type:
            # 判断点击的是鼠标左键还是右键。[0] 为左键，[1] 为右键
            if pygame.mouse.get_pressed()[0]:
                is_mouse_down = True
            elif pygame.mouse.get_pressed()[2]:
                pos = pygame.mouse.get_pos()
                # 获取与右键点击位置相交的精灵，并存储到指定的列表中
                collideSprite = [s for s in sprite_list if s.rect.collidepoint(pos)]
                # 删除相交的精灵
                sprite_list.remove(collideSprite)
        elif pygame.MOUSEBUTTONUP == event.type:
            is_mouse_down = False
    # 去除拖影
    screen.fill((0, 0, 0))
    # 绘制精灵对象
    sprite_list.draw(screen)
    sprite_list.update()
    # 每秒刷新60帧
    clock.tick(60)

    pygame.display.update()

    if is_mouse_down:
        pos = pygame.mouse.get_pos()
        speedx = random.randrange(-5, 5)
        speedy = random.randrange(-5, 5)

        # 创建精灵对象并添加到Group对象中
        scale_smiley = Smiley(pos, speedx, speedy)
        sprite_list.add(scale_smiley)
pygame.quit()
```

---

## 十、游戏编程

本章将生成一个弹跳小球的游戏，小球就是笑脸。在画布底边会有一个挡板，该挡板用来接住小球，不让小球触碰底边。挡板接住小球之后增加得分，小球触碰底边之后减去一次游戏机会，共五次机会。随着得分的增加，小球移动的速度也会增加。

### 1、构建游戏框架
#### (1)、绘制游戏板和游戏部件

##### A: 确定初始状态下挡板的信息

```python
import pygame

# 预置挡板的颜色、宽高、初始坐标
WHITE=(0,0,0)
pad_width=500
pad_height=30
pad_x=400
pad_y=570

```

##### B: 确保光标在挡板的 x 中心

```python
# 获取光标当前所在位置的x坐标
pad_x = pygame.mouse.get_pos()[0]
# pygame的坐标系是从左上角开始的，
# 减去一半的宽度能确保光标与挡板x坐标中心一致
pad_x -= pad_width / 2

```

##### C: 绘制矩形挡板

```python
# 绘制矩形挡板
screen = pygame.display.set_mode([800, 600])
pygame.draw.rect(screen, WHITE, (pad_x, pad_y, pad_width, pad_height))

```

##### D: 画好笑脸和挡板



```python
import pygame

pygame.init()

# 预置挡板的颜色、宽高、初始坐标
WHITE = (255, 255, 255)
pad_width = 500
pad_height = 20
pad_x = 400
pad_y = 550

screen = pygame.display.set_mode([800, 600])

pic = pygame.image.load("CrazySmile.bmp")
pic_x = 0
pic_y = 0

keep_going = True

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False

    screen.blit(pic, (pic_x, pic_y))

    # 获取光标当前所在位置的x坐标
    pad_x = pygame.mouse.get_pos()[0]
    # pygame的坐标系是从左上角开始的，
    # 减去一半的宽度能确保光标与挡板x坐标中心一致
    pad_x -= pad_width / 2
    # 绘制矩形挡板
    pygame.draw.rect(screen, WHITE, (pad_x, pad_y, pad_width, pad_height))

    pygame.display.update()
pygame.quit()

```

运行上述程序之后，会得到如下界面:

![](https://s2.ax1x.com/2019/02/12/kwWSVs.png)

##### E: 让小球动起来

```python
import pygame

pygame.init()

# 画布信息
screen = pygame.display.set_mode([800, 600])
scr_width = screen.get_width()
scr_height = screen.get_height()

# 预置挡板的颜色、宽高、初始坐标
WHITE = (255, 255, 255)
pad_width = 200
pad_height = 20
pad_x = 400
pad_y = 570

# 预置图片信息
pic = pygame.image.load("CrazySmile.bmp")
pic_x = 0
pic_y = 0
speed_x = 5
speed_y = 5
pic_width = pic.get_width()
pic_height = pic.get_height()

keep_going = True
clock = pygame.time.Clock()

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
    # 去除拖影
    screen.fill((0, 0, 0))
    screen.blit(pic, (pic_x, pic_y))

    pic_x += speed_x
    pic_y += speed_y

    if pic_x < 0 or pic_x + pic_width > scr_width:
        speed_x = -speed_x

    if pic_y < 0 or pic_y + pic_height > scr_height:
        speed_y = -speed_y

    # 获取光标当前所在位置的x坐标
    pad_x = pygame.mouse.get_pos()[0]
    # pygame的坐标系是从左上角开始的，
    # 减去一半的宽度能确保光标与挡板x坐标中心一致
    pad_x -= pad_width / 2
    # 绘制矩形挡板
    pygame.draw.rect(screen, WHITE, (pad_x, pad_y, pad_width, pad_height))

    clock.tick(60)
    pygame.display.update()
pygame.quit()

```

#### (2)、记录分数

笑脸碰到挡板之后增加一分，笑脸碰到底边的时候减去一次生命值。

* 如果笑脸的 y 坐标 + 笑脸高度 > 画布高度，此时 生命值 -1
* 如果笑脸的 y 轴底部坐标正好位于挡板上（即：pad_y <= pic_y + pic_height <= pad_y + pad_height），并且笑脸中心线的 x 坐标 处于挡板左端点 x 坐标和右端点 x 坐标之间，表示触碰成功。具体如下图：
	![](https://s2.ax1x.com/2019/02/12/kwhluD.png)
* 只有当笑脸是从上向下触碰到挡板时得分才会+1，此时，speed_y 应该为正数。
* 在代码末尾追加 “\” 表示代码可以折行 

核心代码：

```python
 # 生命值减少逻辑。笑脸 y 坐标 + 高度 超过画布高度必然表示挡板没接到球
    if pic_y + pic_height > scr_height:
        lives -= 1
        speed_y = -speed_y

    # 得分增加逻辑
    if pad_y <= pic_y + pic_height <= pad_y + pad_height and \
            pad_x <= pic_x + pic_width / 2 <= pad_x + pad_width and \
            speed_y > 0:
        # 如果笑脸的底部正好位于挡板上，并且 x 坐标在挡板 x 坐标之内，而且是从上向下落，得分+1
        points += 1
        speed_y = -speed_y
```

完整代码：

```python
import pygame

pygame.init()

# 画布信息
screen = pygame.display.set_mode([800, 600])
scr_width = screen.get_width()
scr_height = screen.get_height()

# 预置挡板的颜色、宽高、初始坐标
WHITE = (255, 255, 255)
pad_width = 200
pad_height = 20
pad_x = 400
pad_y = 570

# 预置图片信息
pic = pygame.image.load("CrazySmile.bmp")
pic_x = 0
pic_y = 0
speed_x = 5
speed_y = 5
pic_width = pic.get_width()
pic_height = pic.get_height()

keep_going = True
clock = pygame.time.Clock()

# 得分和生命值
lives = 5
points = 0

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
    # 去除拖影
    screen.fill((0, 0, 0))
    screen.blit(pic, (pic_x, pic_y))

    pic_x += speed_x
    pic_y += speed_y

    if pic_x < 0 or pic_x + pic_width > scr_width:
        speed_x = -speed_x

    if pic_y < 0:
        speed_y = -speed_y

    # 生命值减少逻辑。笑脸 y 坐标 + 高度 超过画布高度必然表示挡板没接到球
    if pic_y + pic_height > scr_height:
        lives -= 1
        speed_y = -speed_y

    # 得分增加逻辑
    if pad_y <= pic_y + pic_height <= pad_y + pad_height and \
            pad_x <= pic_x + pic_width / 2 <= pad_x + pad_width and \
            speed_y > 0:
        # 如果笑脸的底部正好位于挡板上，并且 x 坐标在挡板 x 坐标之内，而且是从上向下落，得分+1
        points += 1
        speed_y = -speed_y

    # 获取光标当前所在位置的x坐标
    pad_x = pygame.mouse.get_pos()[0]
    # pygame的坐标系是从左上角开始的，
    # 减去一半的宽度能确保光标与挡板x坐标中心一致
    pad_x -= pad_width / 2
    # 绘制矩形挡板
    pygame.draw.rect(screen, WHITE, (pad_x, pad_y, pad_width, pad_height))

    clock.tick(60)
    pygame.display.update()

    print("生命值和得分：", lives, points)
pygame.quit()

```

此时，运行上述程序，可以获取到生命值和得分了，但是还没有将这些信息显示到界面上。

#### (3)、显示得分

* `font = pygame.font.SysFont("Times",24)` 表示创建 Times 字体对象，字号大小为 24 像素
* `text = font.render(draw_str,True,WHITE)` 表示生成一个等待绘制的有具体内容的 Surface 界面对象。 第一个参数表示 要绘制的文本内容，第二个参数表示是否开启抗锯齿，第三个参数表示文本颜色
* `text_rect = text.get_rect()` 表示获取文本所在的矩形区域
* `text_rect.centerx = screen.get_rect().centerx` 表示让文本在屏幕中实现水平居中
* `text.rect.y = 10` 表示文本相距画布顶端 10 像素
* `screen.blit(text,text_rect)` 表示将文本绘制在界面上



#### (4)、完整代码

```python
import pygame

pygame.init()

# 画布信息
screen = pygame.display.set_mode([800, 600])
scr_width = screen.get_width()
scr_height = screen.get_height()

# 预置挡板的颜色、宽高、初始坐标
WHITE = (255, 255, 255)
pad_width = 200
pad_height = 20
pad_x = 400
pad_y = 570

# 预置图片信息
pic = pygame.image.load("CrazySmile.bmp")
pic_x = 0
pic_y = 0
speed_x = 5
speed_y = 5
pic_width = pic.get_width()
pic_height = pic.get_height()

keep_going = True
clock = pygame.time.Clock()

# 得分和生命值
lives = 5
points = 0

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
    # 去除拖影
    screen.fill((0, 0, 0))
    screen.blit(pic, (pic_x, pic_y))

    pic_x += speed_x
    pic_y += speed_y

    if pic_x < 0 or pic_x + pic_width > scr_width:
        speed_x = -speed_x

    if pic_y < 0:
        speed_y = -speed_y

    # 生命值减少逻辑。笑脸 y 坐标 + 高度 超过画布高度必然表示挡板没接到球
    if pic_y + pic_height > scr_height:
        lives -= 1
        speed_y = -speed_y

    # 得分增加逻辑
    if pad_y <= pic_y + pic_height <= pad_y + pad_height and \
            pad_x <= pic_x + pic_width / 2 <= pad_x + pad_width and \
            speed_y > 0:
        # 如果笑脸的底部正好位于挡板上，并且 x 坐标在挡板 x 坐标之内，而且是从上向下落，得分+1
        points += 1
        speed_y = -speed_y

    # 绘制得分和生命值信息
    draw_str = " : " + str(lives) + " , Points : " + str(points)
    # 创建 SysFont 对象
    font = pygame.font.SysFont("Times", 24)
    # 生成绘制文本的 surface 对象
    text = font.render(draw_str, True, WHITE)
    # 获取文本所在矩形区域
    text_rect = text.get_rect()
    # 让文本水平居中
    text_rect.centerx = screen.get_rect().centerx
    # 文本距离画布顶部15
    text_rect.y = 15

    screen.blit(text, text_rect)

    # 获取光标当前所在位置的x坐标
    pad_x = pygame.mouse.get_pos()[0]
    # pygame的坐标系是从左上角开始的，
    # 减去一半的宽度能确保光标与挡板x坐标中心一致
    pad_x -= pad_width / 2
    # 绘制矩形挡板
    pygame.draw.rect(screen, WHITE, (pad_x, pad_y, pad_width, pad_height))

    clock.tick(60)
    pygame.display.update()

    print("生命值和得分：", lives, points)
pygame.quit()

```

运行结果如下：

![](https://s2.ax1x.com/2019/02/12/kw7bPH.png)

### 2、增加难度并结束游戏

#### (1)、游戏结束

![](https://s2.ax1x.com/2019/02/13/k0idkd.png)

* 核心代码：

```python
 # 如果生命值为0.显示目前总得分、停止移动笑脸、提示按 F1 重新开始
    if lives < 1:
        speed_x = speed_y = 0
        draw_str = "Game Over. Your score was: " + str(points)
        draw_str += ", Press F1 to play again"
```

#### (2)、再玩一次

* `if event.type==pygame.KEYDOWN` 判断是否有键盘按键被按下
* `if event.key==pygame.K_F1` 判断被按下的键是不是 F1 键

> 点击 [https://www.pygame.org/docs/ref/key.html](https://www.pygame.org/docs/ref/key.html) 查看 pygame 中完整的按键列表

核心代码：

```python

# 初始化图片的坐标、速度，初始化得分和生命值信息
# 由于开始绘制之前和重置游戏都需要调用该部分，所以抽取为方法
def init_pic_and_lives():
	# 注意这里的 global 修饰符，表示这些是全局变量
    global pic_x, pic_y, speed_x, speed_y, lives, points
    pic_x = 0
    pic_y = 0
    speed_x = 5
    speed_y = 5
    # 得分和生命值
    lives = 5
    points = 0


init_pic_and_lives()

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            # 点击了画布左上角的关闭按钮
            keep_going = False
        elif pygame.KEYDOWN == event.type:
            # 键盘按键被点击了
            if pygame.K_F1 == event.key:
                # 如果 F1 按键被点击了，重置游戏
                init_pic_and_lives()

	#... 其他内容省略
```


#### (3)、加快游戏速度

在本次生命值中，得分越高，移动速度越快；本次生命值被耗费时，游戏速度重置。

##### A: 加快游戏速度

笑脸触碰到画布左边界或者右边界的时候，水平移动速度 * 1.1

笑脸触碰到画布上边界的时候，垂直移动速度 +1

```python

 if pic_x <= 0 or pic_x + pic_width >= scr_width:
        # 笑脸触碰到左边缘或者右边缘的时候，水平移动速度 * 1.1
        speed_x = -speed_x * 1.1

    if pic_y <= 0:
        # 笑脸触碰到上边缘的时候，垂直移动速度+1
        speed_y = -speed_y + 1
```

##### B: 重置游戏速度

损耗一次生命值之后，重置游戏速度

```python
 # 生命值减少逻辑。笑脸 y 坐标 + 高度 超过画布高度必然表示挡板没接到球
    if pic_y + pic_height > scr_height:
        lives -= 1
        # 损耗生命值时，重置游戏速度
        speed_y = -5
        speed_x = 5
```

##### C: 损失声明值之后，修改笑脸底部边界

笑脸移动速度过快时，可能会移动到画布底边界以外，此时从底部再向上回弹可能会出现的极端情况是：还没有移动到底部边界上方，就频繁触发了 `if pic_y + pic_height > scr_height:`, 导致生命值连续被损耗了。所以——

在损耗一次生命值之后，重置 笑脸的 y 坐标，保证笑脸回弹时底部y坐标在画布以内。

```python
 生命值减少逻辑。笑脸 y 坐标 + 高度 超过画布高度必然表示挡板没接到球
    if pic_y + pic_height > scr_height:
        lives -= 1
        # 损耗生命值时，重置游戏速度
        speed_y = -5
        speed_x = 5
        # 重置笑脸 y 坐标，避免出现损耗生命之后还没有回到底部边界上方就频繁损耗生命的情况
        pic_y = scr_height - pic_height
```

##### D: 完整代码

```python
import pygame

pygame.init()

# 画布信息
screen = pygame.display.set_mode([800, 600])
scr_width = screen.get_width()
scr_height = screen.get_height()

# 预置挡板的颜色、宽高、初始坐标
WHITE = (255, 255, 255)
pad_width = 200
pad_height = 20
pad_x = 400
pad_y = 570

# 预置图片信息
pic = pygame.image.load("CrazySmile.bmp")
pic_width = pic.get_width()
pic_height = pic.get_height()


def init_pic_and_lives():
    # 初始化图片的坐标、速度，初始化得分和生命值信息
    # 由于开始绘制之前和重置游戏都需要调用该部分，所以抽取为方法
    # 注意这里的 global 修饰符，表示这些是全局变量
    global pic_x, pic_y, speed_x, speed_y, lives, points
    pic_x = 0
    pic_y = 0
    speed_x = 5
    speed_y = 5
    # 得分和生命值
    lives = 5
    points = 0


init_pic_and_lives()

keep_going = True
clock = pygame.time.Clock()

while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            # 点击了画布左上角的关闭按钮
            keep_going = False
        elif pygame.KEYDOWN == event.type:
            # 键盘按键被点击了
            if pygame.K_F1 == event.key:
                # 如果 F1 按键被点击了，重置游戏
                init_pic_and_lives()

    # 去除拖影
    screen.fill((0, 0, 0))
    screen.blit(pic, (pic_x, pic_y))

    pic_x += speed_x
    pic_y += speed_y

    if pic_x <= 0 or pic_x + pic_width >= scr_width:
        # 笑脸触碰到左边缘或者右边缘的时候，水平移动速度 * 1.1
        speed_x = -speed_x * 1.1

    if pic_y <= 0:
        # 笑脸触碰到上边缘的时候，垂直移动速度+1
        speed_y = -speed_y + 1

    # 生命值减少逻辑。笑脸 y 坐标 + 高度 超过画布高度必然表示挡板没接到球
    if pic_y + pic_height > scr_height:
        lives -= 1
        # 损耗生命值时，重置游戏速度
        speed_y = -5
        speed_x = 5
        # 重置笑脸 y 坐标，避免出现损耗生命之后还没有回到底部边界上方就频繁损耗生命的情况
        pic_y = scr_height - pic_height

    # 得分增加逻辑
    if pad_y <= pic_y + pic_height <= pad_y + pad_height and \
            pad_x <= pic_x + pic_width / 2 <= pad_x + pad_width and \
            speed_y > 0:
        # 如果笑脸的底部正好位于挡板上，并且 x 坐标在挡板 x 坐标之内，而且是从上向下落，得分+1
        points += 1
        speed_y = -speed_y

    # 绘制得分和生命值信息
    draw_str = "Lives : " + str(lives) + " , Points : " + str(points)

    # 如果生命值为0.显示目前总得分、停止移动笑脸、提示按 F1 重新开始
    if lives < 1:
        speed_x = speed_y = 0
        draw_str = "Game Over. Your score was: " + str(points)
        draw_str += ", Press F1 to play again"

    # 创建 SysFont 对象
    font = pygame.font.SysFont("Times", 24)
    # 生成绘制文本的 surface 对象
    text = font.render(draw_str, True, WHITE)
    # 获取文本所在矩形区域
    text_rect = text.get_rect()
    # 让文本水平居中
    text_rect.centerx = screen.get_rect().centerx
    # 文本距离画布顶部15
    text_rect.y = 15

    screen.blit(text, text_rect)

    # 获取光标当前所在位置的x坐标
    pad_x = pygame.mouse.get_pos()[0]
    # pygame的坐标系是从左上角开始的，
    # 减去一半的宽度能确保光标与挡板x坐标中心一致
    pad_x -= pad_width / 2
    # 绘制矩形挡板
    pygame.draw.rect(screen, WHITE, (pad_x, pad_y, pad_width, pad_height))

    clock.tick(60)
    pygame.display.update()

    print("生命值和得分：", lives, points)
pygame.quit()

```

### 3、添加更多的功能

在第九章中写过一个 点击鼠标左键增加笑脸，点击右键消除笑脸的小游戏。本章要做的就是在消除笑脸时增加音效，并统计总共增加和消除了多少个笑脸。

左键增加笑脸，右键消除笑脸的代码如下：

```python

# 带有音效的笑脸爆炸游戏

import random

import pygame

pygame.init()
screen = pygame.display.set_mode([800, 600])
pic = pygame.image.load("CrazySmile.bmp")

keep_going = True
is_mouse_down = False
sprite_list = pygame.sprite.Group()


class SmileySprite(pygame.sprite.Sprite):
    # 位置坐标
    pos = (0, 0)
    # 位移速度
    velocity_x = 0
    velocity_y = 0
    # 定义图片的宽度
    pic_width = 100

    # 定义构造函数
    def __init__(self, pos, speedx, speedy):
        # 先调用父类的init函数
        pygame.sprite.Sprite.__init__(self)
        self.pos = pos

        self.velocity_x = speedx
        self.velocity_y = speedy

        self.image = pic
        # 控制图片的大小范围：15-100
        self.pic_width = random.randint(15, 100)
        # 获取真实图片
        self.image = pygame.transform.scale(self.image, (self.pic_width, self.pic_width))
        self.rect = self.image.get_rect()
        # 控制鼠标光标在图片中心点
        self.rect.x = pos[0] - self.pic_width / 2
        self.rect.y = pos[1] - self.pic_width / 2

    # 重写update函数
    def update(self):
        self.rect.x += self.velocity_x
        self.rect.y += self.velocity_y

        screen_width = screen.get_width()
        screen_height = screen.get_height()

        # 如果到达画布左边界或者右边界，位移速率取反
        if self.rect.x <= 0 or self.rect.x >= screen_width - self.pic_width:
            self.velocity_x = -self.velocity_x
        if self.rect.y <= 0 or self.rect.y >= screen_height - self.pic_width:
            self.velocity_y = -self.velocity_y


while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
        elif pygame.MOUSEBUTTONDOWN == event.type:
            if pygame.mouse.get_pressed()[0]:
                # 如果是鼠标左键被按下了
                is_mouse_down = True
            elif pygame.mouse.get_pressed()[2]:
                # 如果鼠标右键被按下了，获取相交的精灵，并消除
                pos = pygame.mouse.get_pos()
                collide_sprites = [s for s in sprite_list if s.rect.collidepoint(pos)]
                sprite_list.remove(collide_sprites)
        elif pygame.MOUSEBUTTONUP == event.type:
            is_mouse_down = False
    if is_mouse_down:
        # 将精灵添加到group中
        pos = pygame.mouse.get_pos()
        speed_x = random.randint(-5, 5)
        speed_y = random.randint(-5, 5)

        # 每个精灵必须有位置，有移动速度
        sprite = SmileySprite(pos, speed_x, speed_y)
        sprite_list.add(sprite)

    screen.fill((0, 0, 0))
    # screen.blit(pic, (0, 0))
    sprite_list.update()
    sprite_list.draw(screen)
    pygame.display.update()

pygame.quit()

```

#### (1)、使用 Pygame 添加声音

首先将 .wav 声音文件拷贝到源码文件同目录下

```python
# 播放声音需要借助 pygame.mixer
pygame.mixer.init()
# 生成声音对象
blap_sound = pygame.mixer.Sound("blap.wav")
# 播放声音对象
blap_sound.play()

```
#### (2)、跟踪和记录玩家进度

统计一共生成了多少个笑脸、一共消除了多少个笑脸，并计算消除的比率。然后将这些信息显示在界面上。

新增时+1，消除时批量减少。计算比率时主要除数不能为0


#### (3)、完整代码：

运行下列程序时，点击左键会在画布中新增笑脸，点击右键时如果触摸到了笑脸则会消除笑脸并播放音效。

```python
# 带有音效的笑脸爆炸游戏

import random

import pygame

pygame.init()
screen = pygame.display.set_mode([800, 600])
pic = pygame.image.load("CrazySmile.bmp")

keep_going = True
is_mouse_down = False
sprite_list = pygame.sprite.Group()

# 生成声音对象
pygame.mixer.init()
blap_sound = pygame.mixer.Sound("blap.wav")

# 记录新增和消除的数量
count_smiley = 0
count_poped = 0
font_hint = pygame.font.SysFont("Arial", 24)
WHITE = (255, 255, 255)


class SmileySprite(pygame.sprite.Sprite):
    # 位置坐标
    pos = (0, 0)
    # 位移速度
    velocity_x = 0
    velocity_y = 0
    # 定义图片的宽度
    pic_width = 100

    # 定义构造函数
    def __init__(self, pos, speedx, speedy):
        # 先调用父类的init函数
        pygame.sprite.Sprite.__init__(self)
        self.pos = pos

        self.velocity_x = speedx
        self.velocity_y = speedy

        self.image = pic
        # 控制图片的大小范围：15-100
        self.pic_width = random.randint(15, 100)
        # 获取真实图片
        self.image = pygame.transform.scale(self.image, (self.pic_width, self.pic_width))
        self.rect = self.image.get_rect()
        # 控制鼠标光标在图片中心点
        self.rect.x = pos[0] - self.pic_width / 2
        self.rect.y = pos[1] - self.pic_width / 2

    # 重写update函数
    def update(self):
        self.rect.x += self.velocity_x
        self.rect.y += self.velocity_y

        screen_width = screen.get_width()
        screen_height = screen.get_height()

        # 如果到达画布左边界或者右边界，位移速率取反
        if self.rect.x <= 0 or self.rect.x >= screen_width - self.pic_width:
            self.velocity_x = -self.velocity_x
        if self.rect.y <= 0 or self.rect.y >= screen_height - self.pic_width:
            self.velocity_y = -self.velocity_y


while keep_going:
    for event in pygame.event.get():
        if pygame.QUIT == event.type:
            keep_going = False
        elif pygame.MOUSEBUTTONDOWN == event.type:
            if pygame.mouse.get_pressed()[0]:
                # 如果是鼠标左键被按下了
                is_mouse_down = True
            elif pygame.mouse.get_pressed()[2]:
                # 如果鼠标右键被按下了，获取相交的精灵，
                pos = pygame.mouse.get_pos()
                collide_sprites = [s for s in sprite_list if s.rect.collidepoint(pos)]

                # 相交精灵数大于0，播放音效
                if len(collide_sprites) > 0:
                    blap_sound.play()
                    count_poped += len(collide_sprites)

                # 移除精灵
                sprite_list.remove(collide_sprites)
        elif pygame.MOUSEBUTTONUP == event.type:
            is_mouse_down = False
    if is_mouse_down:
        # 将精灵添加到group中
        pos = pygame.mouse.get_pos()
        speed_x = random.randint(-5, 5)
        speed_y = random.randint(-5, 5)

        # 每个精灵必须有位置，有移动速度
        sprite = SmileySprite(pos, speed_x, speed_y)
        sprite_list.add(sprite)
        count_smiley += 1

    screen.fill((0, 0, 0))
    # screen.blit(pic, (0, 0))
    sprite_list.update()
    sprite_list.draw(screen)

    # 绘制提示信息
    str_hint = "Simley count : " + str(count_smiley)
    str_hint += ". Poped count : " + str(count_poped)
    # 避免除数为0
    if count_smiley > 0:
        str_hint += ". Percent : "
        str_hint += str(round(count_poped / count_smiley * 100))
        str_hint += "%"
    # 创建要绘制内容的 surface——界面对象
    text = font_hint.render(str_hint, True, WHITE)
    text_rect = text.get_rect()
    # 水平居中，垂直偏移10
    text_rect.centerx = screen.get_rect().centerx
    text_rect.y = 10
    # 将内容绘制在指定区域
    screen.blit(text, text_rect)
    pygame.display.update()

pygame.quit()

```

---

## 十一：补充——模块

我们编写的每一个 .py 文件都是一个模块，像 turtle(海龟)、pygame 都是模块。

在文件中引入自己的模块时也是遵循 `import 文件名` 的格式。导入之后就可以直接使用其中的变量和方法了。或者也可以使用 `import 文件名 as 别名` 的格式，这样在使用导入的模块时，就可以通过 别名 调用内部的函数和变量了。


> 20190213 完结。
