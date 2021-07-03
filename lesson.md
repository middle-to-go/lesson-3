





















[TOC]





























---



 









📽️ - посмотри воркшоу

⚗️ - проведи эксперимент

🔬 - изучи внимательно

📖 - прочитай документация

🪙 - подумай о сложности

🐞 - запомни ошибку

🔨 - запомни решение

🏔️ - обойди камень предкновенья

⏰ - сделай перерыв

🏡 - попробуй дома

💡 - обсуди светлые идеи

🙋 - задай вопрос

⚡ - запомни панику











---





















## Множества



























---

- Подводный камень

```go
map[uint]bool

type struct{} void
```

- Инициализация

```go
var userIds map[uint]struct{} // nil - zero value

var userIds map[uint]struct{}{}

userIds := make(map[uint]struct{})

userIds := make(map[uint]struct{}, len(users)) // with capacity

println(userIds)
```

```go
userIds := map[uint]struct{}{1, 2, 3} // compilation error 🐞

userIds = map[uint]struct{}{ // 🔨
  1: {},
  2: {},
  3: {}, // struct{}
}
```

- Чтение

```go
userIds := map[uint]struct{}{1: {}}
println(userIds[1])
println(userIds[2]) // zero-value
```

- [ ] Сравнение двух множеств 🏡 💡

---

- Итерирование

```go
for userId, _ := range userIds { // 🐞 semantic error
}

for userId := range userIds {
}
```

- Удаление

```go
if _, found := userIds[1]; found { // 🙋 semantic error ?
  delete(userIds, 1)
}

delete(userIds, 1)
```

- Копирование

```go
src := map[uint]struct{}{1: {}}

dst := src
// dst[2] = struct{}{}
// println(src)
```

```go
src := map[uint]struct{}{1: {}}

dst := make(map[uint]struct{}, len(src))

for key := range src {
  dst[key] = struct{}{}
}
// dst[2] = struct{}{}
// println(src)
```

---























## Строки

























---

> In Go, a **string** is in effect a read-only <u>slice</u> of bytes

> Any slice in Go stores the length (in bytes), so you don't have to care about the cost of the `len` operation : there is no need to count 🪙
>
> Go strings aren't **null** terminated, so you don't have to remove a null byte, and you don't have to add `1` after slicing by adding an empty string.

Литералы

- symbol
- text
- multiline text

Как **slice**

```go
const nihongo = "日本語"
fmt.Printf("len: %d\n", len(nihongo))
fmt.Printf("cap: %d\n", cap(nihongo)) // 🙋 скомпилируется ?
```

- Индексирование

```go
const text = "日本語"
fmt.Println(string(text[2])) // 🙋 что будет выведено на экран ?
```

- Конвертация

```go
b := []byte("ABC€")
fmt.Println(b) // [65 66 67 226 130 172]

s := string([]byte{65, 66, 67, 226, 130, 172})
fmt.Println(s) // ABC€
```

---

Как **string**

- Индексирование

```go
const text = "hello"
fmt.Println(text[0])
```

```go
func replaceAtIndex(in string, r rune, i int) string {
    out := []rune(in)
    out[i] = r
    return string(out)
}
```

- Итерирование

```go
for _, runeValue := range nihongo {
	fmt.Println("%u", runeValue)
}
```

- Конкатенация

```go
"123" + "456"
```

- Сравнение

```go
// Compare is included only for symmetry with package bytes. 
// It is usually clearer and always faster to use the built-in  
// string comparison operators ==, <, >, and so on.
if str1 == str2 {
}
```



---

- Пакет **strings**

|      | Go                        | C++  |
| ---- | ------------------------- | ---- |
| 1    | `Contains`/`ContainsAny`  |      |
| 2    | `Count`                   |      |
| 3    | `HasPrefix`/`HasSuffix`   |      |
| 4    | `Index`/`LastIndex`       |      |
| 5    | `IndexAny`/`LastIndexAny` |      |
| 6    | `Replace`/`ReplaceAll`    |      |
| 7    | `Title`/`ToTitle`         |      |
| 8    | `ToLower`/`ToUpper`       |      |
| 9    | `Trim`                    |      |
| 10   | `TrimLeft`/`TrimRight`    |      |
| 11   | `TrimPrefix`/`TrimSuffix` |      |
| 12   | `TrimSpace`               |      |
| 13   | `Join`/`Split`            |      |
| 14   | `Repeat`                  |      |
| ...  | ...                       |      |

> The rule Title uses for word boundaries does not handle Unicode punctuation properly. 🐞

---























## Отложенные вызовы

























---

> A `defer` statement postpones the execution of a function until the surrounding function returns, either normally <u>or</u> through a panic

```go
func main() {
  for i := 0; i < 5; i++ {
		defer fmt.Println(i) // 🙋 что будет выведено на экран ?
	}
	fmt.Println(5)
}
```

```go
for {
	file := openFile(filePath)
	defer closeFile(file) // 🐞 semantic error
}
fmt.Println(5)
```

```go
func main() {
    i := 0
    defer fmt.Println(i) // ⚗️ проверь с другими типами
    i++
}
```

```go
func main() {
  defer fmt.Println("world")
  panic("stop")
  fmt.Println("hello")
}
```

```go
func getName() (result string) {
    defer func() { result = "Veronika" }()
    return "Vika"
}
```

---

```go
func getName() (result string) {
    defer func() { result = "Veronika" }()
    result = "Vika"
    return
}
```

Восстановление работы после паники

```go
package main

import "fmt"

func recovering() {
  if obj:=recover(); obj != nil {
    fmt.Println(obj)
  }
  fmt.Println("recovered")
}

func executePanic() {
  defer recovering()
  panic("stop")
  fmt.Println("after panic")
}

func main() {
  executePanic()
  fmt.Println("end")
}

// stop
// recovered
// end
```

---





















## **Error**

































---

- `error`

```go
err := errors.New("missed user") // return error
fmt.Println(err.Error())
```

```go
err = fmt.Errorf("... %w ...", ..., err, ...)
fmt.Errorf("cant describe user with id <%d> due: %w", userId, err)
```

```go
// import "errors", "os" and "io/fs"
if _, err := os.Open("non-existing"); err != nil {
  var pathError *fs.PathError
  if errors.As(err, &pathError) {
    fmt.Println("Failed at path:", pathError.Path)
  } else {
    fmt.Println(err)
  }
}
```

```go
if errors.Is(err, fs.ErrExist) {
}
// vs
if err == fs.ErrExist { // 🐞 semantic error
}
```

```go
var pathError *fs.PathError
if errors.As(err, &pathError) {
	fmt.Println(perr.Path)
}
// vs
if pathError, ok := err.(*fs.PathError); ok { // 🐞 semantic error
	fmt.Println(pathError.Path)
}
```

---

```go
type Error interface {
  Error() string
}
```

Наименование:

- **err**

- **specificError**
- **err1**, **err2**, ... 🏔️



Error return design pattern:

```go
func foo(...) (result, err) {
 	// ...
}

func minmax(a,b uint) (min uint, max uint, error) {
  return 0, 0, errors.New("not implemented")
}

func minmax(a,b uint) (*MinMax, error) {
  return 0, 0, errors.New("not implemented")
}
```

- численный тип
- строка
- **array**
- **slice**
- **map**
- пользовательский тип
-  ...

Составной результат **vs** множества значений ? 🙋

---





















## Структуры 





















---

Создание пользовательского типа

```go
type uint8 TaskDifficulty

const (
  Beginner TaskDifficulty = iota
  Easy
  Normal
  Hard
)

type Task struct {
  Description  string
  Difficulty   TaskDifficulty
}
```

```go
func main() {
  
  task := Task{
    Description: "revert list",
    Difficulty: Beginner,
  }
  
  fmt.Println(task.Description)
}
```

Объявления пользовательского типа:

- C перечислением полей на одной строке
- Поля как _ 💡
- Объявление внутри функции
- Анонимные структуры

Доступ к [полям](https://github.com/cpp-to-go/lesson_3/structs/field.go)

- [ ] Область видимости структур и полей 🏡	

---

```go
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs())
	v.Scale(10)
	fmt.Println(v.Abs())
}
```

- Пример простого метода
- Область видимости
- Вариации приемника
  - **value receiver**
  - **pointer receiver**
- Функия, как метод
- Пример безопасной обработки **nil** приемника
- Статические методы

> You can declare methods with pointer receivers
>
> This means the receiver type has the literal syntax `*T` for some type `T`
>
> Also, `T` cannot itself be a pointer such as `*int`

---



















## Testing





















