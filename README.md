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
