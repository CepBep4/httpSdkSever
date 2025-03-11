#Переменные
---
1. [[Go#^e2749b |  Создание переменных]]
2. [[Go#^8145d5| Типы данных]]
3. [[Go#^30fb00 | Пользовательские типы]]
4. [[Go#^cc483c | Виды массивов]]


 ### Создание переменных c примерами
>``` go
>//Проиницилизировать переменную
>var <имя переменной> <тип переменной>;
 >
 >//Проиницилизировать с присвоением
 >var <имя переменной> <тип переменной> = <значение>
>
>//Кратко задать
>var <имя переменной> = <значение>
><имя переменной> := <значение>
>```
^e2749b

>[!NOTE] **Все типы данных**
>- **int8** *представляет целое число от -128 до 127 и занимает в памяти 1 байт (8 бит)*
>- **int16** *представляет целое число от -32768 до 32767 и занимает в памяти 2 байта (16 бит)*
>- **int32** *представляет целое число от -2147483648 до 2147483647 и занимает 4 байта (32 бита)*
>- **int64** *представляет целое число от –9 223 372 036 854 775 808 до 9 223 372 036 854 775 807 и занимает 8 байт (64 бита)*
>- **uint8** *представляет целое число от 0 до 255 и занимает 1 байт*
>- **uint16** *представляет целое число от 0 до 65535 и занимает 2 байта*
>- **uint32** *представляет целое число от 0 до 4294967295 и занимает 4 байта*
>- **uint64** *представляет целое число от 0 до 18 446 744 073 709 551 615 и занимает 8 байт*
>- **byte** *синоним типа `uint8`, представляет целое число от 0 до 255 и занимает 1 байт*
>- **rune** *синоним типа `int32`, представляет целое число от -2147483648 до 2147483647 и занимает 4 байта*
>- **int** *представляет целое число со знаком, которое в зависимости о платформы может занимать либо 4 байта, либо 8 байт. То есть соответствовать либо int32, либо int64*
>- **uint** *представляет целое беззнаковое число только без знака, которое, аналогично типу int, в зависимости о платформы может занимать либо 4 байта, либо 8 байт. То есть соответствовать либо uint32, либо uint64*
^8145d5

###### Пользовательские типы данных

^30fb00

`struct | type | func(<type>) | interface | any`
```Go
//Структура
type variable struct{
	param1 int
	param2 string
};

//Пользовательский тип
type Integer int;

//Функция
type MyFunc func(int, int)bool;

//Интерфейс
type MyInterface interface {
	...
}

//Универсальный тип данных
var a = interface{}
```

###### Массивы

^cc483c

`array | slice | map`
```go
array := [3]{1,2,3}
slice := []{1,2,3,...}
map := [string]string{"key":"value"}
```

#Функции 
---
1. Простые функции
2. Анонимные функции

### Что подразумевается под простой функцией?
>Простая функция создаётся вне основной функции main, при этом её область вдимости ограничена её параметрами и телом функции, также в качестве параметров передаются копии объектов

###### Синтаксис
```go
fun(<имя аргумента> <тип аргумента>) <имя функции> (<тип возврата>){
	...
}
```

###### Пример 
```go
func main(){
	Print("Hello, world!")
}

func Print(text string){
	fmt.PrintLn(text)
}
```
###### Комментарий
Если в скобках в конце указать имя переменной и её тип, тогда в `return` можно не указывать что нужно вернуть



### Что подразумевается под анонимной функцией?
>Анонимная функция может создаваться как внутри main, так и снаружи, область видимости такой функции никак не ограничена

###### Синтаксис
```go
f := fun(<имя аргумента> <тип аргумента>) (<тип возврата>){
	...
}
```

###### Пример 
```go
func main(){
	var print = func(text string){
		fmt.PrintLn(text)
	}

	print("Hello, world!")
}
```
#SeverHttpSdkGo
--
**VERSION**: *0.1 Beta*
### Структура
```go
type AppRouter struct {
	ServerPort   int
	TemplatePath string
	StaticPath   string
	Routers      map[string]func(http.ResponseWriter, *http.Request)
	CorsOrigin Cors
}
```
**Основной тип для задания основного конфига Rest-Api сервиса**
*==ServerPort==*, задаёт порт прослушивания
*==TemplatePath==*, задаёт папку где хранятся страницы
*==StaticPath==*, задаёт папку со статикой
*==Routers==*, содержит логику обработки запросов по пути url
*==CorsOrigin==*, позволяет установить Cors

```go
type AnswerResponse struct {
	Writer      *http.ResponseWriter
	TypeAnswer  string
	Response    any
}
```
**Структура формирует ответ сервера**
*==Wrtiter==*, ссылка на http.ResponseWriter, получается
*==TypeAnswer==*, тип ответа `json` | `html`
*==Response==*, объект возврата, принимает либо string либо ссылку на созданную(не пустую) структуру

```go
AppRouter.Listen()
```
Функция добавляет в список прослушивания все обозначенные Routers

```go
app.Routers["/"] = func(w http.ResponseWriter, r *http.Request) {
	...
}
```
Внутри данного метода содержится логика обработки запроса
### Simple example
```go
package main

import "backend/pkg/router"

func main(){
	//Создаём объект приложения
	app := router.AppRouter{
		ServerPort:    5000,
		TemplatePath:  "templates", //Можно указать любой путь
		StaticPath:    "static", //Аналогично тут
	}

	app.Routers["/index"] = func(w http.ResponseWriter, r *http.Request){
		//Возвращаем ответ
		app.Answer(router.AnswerResponse{
			Writer:     &w,
			TypeAnswer: "html",
			Response:   "index.html",
		})
	}
	
	app.Routers["/hello"] = func(w http.ResponseWriter, r *http.Request){
		//Формируем структуру ответа
		type helloMessage struct {
			Message string `json:"message"` //Используем структурный тег json
		}

		//Заполняем её
		var message helloMessage = helloMessage{
			Message: "hello",
		}

		//Возвращаем
		app.Answer(router.AnswerResponse{
			Writer:     &w,
			TypeAnswer: "json",
			Response:   message,
		})
	}

	app.LoadStatic() //Загружаем статику
	app.Listen() //Устанавливаем на прослушивание все ендпоинты
	app.CorsOrigin.SetBase() //Устанавливаем базовое значение Cors
	app.Run() //Запускаем приложение
}

```
*bash log*
```bash
Starting server. PORT:5000
```

#Структуры
---
1. Структурные теги
2. Создание структур
3. Наследование структур
4. Инициализация структур

#### Структурные теги
\`json:"message"\`

#### Создание структур
```go
type <name> struct{
	<name_property> <type_property> <struct tag>
}
```
#### Наследование структур
```go
type Users struct{
	Users []User
}

type User struct{
	fisrt_name: string
	last_name:  string
}

var users Users = Users{
	Users: []User{
		{
			first_name: "Alica",
			last_name:  "Kadge",
		},
		{
			first_name: "Bob",
			last_name:  "Bridg",
		}
	}
}
```
#### Инициализация структур
```go
var <name> <type_struct> = <type_struct>{
	<name_property>: <value>,
}
```


#Методы
---
Создание метода
```go
type BaseStruct struct{
	User string
}

//Создаём метод
func(self *BaseStruct) <name_method> (<params> <type_parms>)<return_type>{
	...
}
```
Вызов метода производится через имя структуры и обращению к ней через точку, подобно этому примеру `<name_struct>.<name_method>(<params>)`
##### Простой пример
```go
type BaseStruct struct{
	User string
}

//Создаём метод
func(self *BaseStruct) GetName (){
	fmt.PrintLn("Имя пользователя: "+self.User)
}

//Создаём экземпляр структуры
var bs BaseStruct = BaseStruct{
	User: "Alice",
}

bs.GetName()
```
```bash
Имя пользователя: Alice
```
