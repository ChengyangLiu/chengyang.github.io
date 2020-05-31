# go基础学习
>_Belonging to: program language_

>_Written date: 2020.5.12_

>_All knowledge is for learning use only, not for commercial use, and the author reserves all rights._

### 一、包

```GO
shell script main //当前包名，程序从main包运行

import ( //导入包
        "fmt"
        "math"
)

func main() {
        fmt.Println("Hello", math.Pi) //其他包内的大写字母开头的成员才能被访问
}`
```
```shell script
go mod init 初始化生成go.mod文件，用于管理整个项目的包依赖。（依赖管理太便利了，再也不用像C++一样手动配依赖了）
```

### 二、变量

```GO
var a float64  // t T 格式，命名在前，类型在后，未初始化参数默认为nil/0/false/""
// var声明，可放于func外。
const Pi = 3.14 //常量，只需const。（C中需要const加具体类型）
func main() {
        a = float64(1)//显式类型转换。（C中是隐式，或(float) Pi,不加括号会被当作变量声明)
        b := 2 //简洁赋值语句，通过右值类型推出变量类型。只可用于函数内
        fmt.Println(a, b, Pi)
}
func split(a, b int) (x, y int) {} //支持多返回值
func(func(int,int) int, int) func(int, int) int {} //函数作参数时清晰，c++惨不忍睹
```

### 三、控制语句

```GO
// 循环，只有for关键字
for i := 0; i < 10; i++ {} //和C没啥区别
for sum < 1000 {} //等价于while
for {} //等价于while(true)

//条件，if和switch
if v := 0; v < lim { //可加简单语句，v的生存周期是整个if-else语句
} else {}
switch a := "a"; a { //可加简单语句
    case "a": //case不必须是整数常量。（C中必须是int量，char也等价于int，不支持string）
        fmt.Println("a") //若case匹配，执行后退出，相当于C中加了break
    default:
        fmt.Printf("others")
}
//switch后面无条件的话，等价于多组if-elseif判断
```

### 四、指针和结构体

```GO
type Vertex struct { //结构体定义
        X int
        Y int
}

func main() {
        v := Vertex{1, 2} //简洁赋值
        p := &v //p为指向v的指针
        p.X = 1e9 //该语句等价于(*p).X = 1e9。指针可用.直接访问结构体成员。（C用->）
        fmt.Println(v)
}
```

### 五、数组和切片

```GO
func main() {
    q := [4]int{1,2,3,4} //创建长度为4的数组
    s := q[:] //返回q的切片
    var s []int //nil，无底层数组，len和cap都为0
    s = []int{1,2,3,4} //先创建长度为4的数组，再返回切片s，切片类似引用，不拷贝底层数据
    len(s) //切片的长度，即包含元素个数
    cap(s) //切片容量，从第一个元素到最后一个元素的数量
    b := make([]int, 0, 5) //make创建切片，len(b)=0, cap(b)=5
    s = append(s, 2, 3, 4) //添加元素
    for index, value := range s {} //range遍历切片，index索引，value值，不需要可用_忽略
}
```


### 六、map

```GO
func main() {
    m := make(map[T1]T2) //make创建一个map。（STL中语法是map<T1,T2> m;）
    value, ok := m[key] //查找key是否存在，存在ok==true，返回value，否则ok==false
    m[key] = value //添加元素
    delete(m, key) //删除元素
}
```

### 七、逃逸

```GO
func main() {
    var p *int
    if true {
        y := new(int)  //y生命周期仅在大括号内，虽用new声明但依然在栈上分配空间
        *y = 3
        m := *y    //本来变量m的生命周期在花括号内
        p = &m    //但是因为p指向了m，所以使得m发生了逃逸，生命周期跳出原有区域
    }
    fmt.Println(*p)
}
//go的GC会根据变量路径是否可追溯来决定是否回收它
//逃逸的概念使得var和new对内存分配不一定是栈和堆空间，栈上的变量发生逃逸则会导致一次变量拷贝
````