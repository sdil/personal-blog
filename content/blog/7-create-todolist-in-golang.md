---
title: "Build a Todolist API Server in Golang"
date: 2020-02-03
slug: "golang-todolist"
description: "Build a Todolist API Server in Golang"
keywords: ["Golang", "Programming"]
draft: false
tags: ["Golang"]
math: false
toc: true
---

I always wanted to learn Golang for a long time. According to the StackOverflow survey, Golang is the  **3rd most wanted programming language**  in the year 2019. Coming from a Python background, learning a new programming language like Golang gives me a unique perspective on how to write code. I was inspired by a [helpful tutorial post](https://keiran.scot/building-a-todo-api-with-golang-and-kubernetes-part-1-introduction/) by Keiran Scot. The following tutorial is heavily influenced by Keiran’s tutorial but made to use MySQL instead of Mongo as part of my exercise to learn Golang.

Personally, **I believe Golang is the new Java**, where many open source projects (e.g., Jaeger, Kubernetes, Docker, InfluxDB, Serf, etc.) are now written in Golang. In contrast, the older projects (e.g., Apache Kafka, Apache Hadoop, Apache Spark, etc.) are written in Java. I wish I could participate and contribute to OSS Golang projects in the future.

There are many advantages of Golang compared to other languages, including:

-   **Golang is blazing fast**  
    See it yourself  [https://benchmarksgame-team.pages.debian.net/benchmarksgame/fastest/go.html](https://benchmarksgame-team.pages.debian.net/benchmarksgame/fastest/go.html)
-   **Golang is built for concurrency**  
    Golang comes with Goroutine that is made to tackle this problem. I was mesmerized with this feature when I first learned about Golang. Unfortunately, we won’t be seeing how goroutine works in this in this tutorial yet.
-  **Golang is statically typed language**
    Coming from a Python background, I find this feature in Golang is a bit irritating at first. However, writing in statically type programming language means that you’ll introduce fewer bugs because you’ll make lesser mistakes on interpreting and passing the data.
-   **Go compiles the program into a single binary**  
    When you’re building an application in Go, you can always run your everywhere. You do not have to worry about its dependencies, because all of them are bundled together.
-   **Golang is an opinionated language**  
    There is a rigorous way on how to write a Golang code. This means there is less variation on how people should write code in Golang, which eventually implies code consistency, readability, and maintainability. For a big and complex project, this is very helpful.
-   You can read  [here](https://yourbasic.org/golang/advantages-over-java-python/)  more about its advantages.

# Prerequisites

-   [Golang](https://golang.org/doc/install)  &  [Docker](https://docs.docker.com/install/)  installed in your system
-   A basic Golang & JQuery knowledge

I have prepared a frontend part for this app, so you don’t have to worry about this and focus on writing the Golang code. All you need is clone the repository in Step #7.

# What we’ll build

We will build a classic and simple Golang API server that connects to a Frontend page. Our Golang API server will use:

-   MySQL as our database
-   GORM as ORM to interacting with our database
-   Request router using  `gorilla/mux`
-   Logrus for logging

## How our entire app will work
![System archictecture](/blog/7/architecture.png)

We will be building what’s on the right side of the diagram. The specifications for our API Server are:

-   It listens to port 8000 on localhost
-   It has 5 endpoints:  `healthz`,  `createItem`,  `getCompletedItems`,  `getIncompleteItems`,  `updateItem`and  `deleteItem`
-   TodoItem model consists of Id, Description and Completed status attributes.

The web frontend that I have prepared will execute AJAX requests to  `localhost:8000`  for all operations. Check this JS script to see how it works first. The initial code was made by  [themaxsandelin](https://github.com/themaxsandelin).

[JS Source Code](https://github.com/sdil/todo/tree/master/resources/js)

# Let’s begin

## Step 1: Bootstrap a project

First, let’s create a new directory and install `gorilla/mux` and `logrus` packages in the directory

```shell
$ mkdir todolist-mysql-go  
$ cd todolist-mysql-go  
$ go get -u github.com/gorilla/mux  
$ go get -u github.com/sirupsen/logrus
```

Now create`todolist.go`  file with the following content:

```go
package main
import (
	"io"
	"net/http"
	"github.com/gorilla/mux"
	log "github.com/sirupsen/logrus"
)

func Healthz(w http.ResponseWriter, r *http.Request) {
	log.Info("API Health is OK")
	w.Header().Set("Content-Type", "application/json")
	io.WriteString(w, `{"alive": true}`)
}

func init() {
	log.SetFormatter(&log.TextFormatter{})
	log.SetReportCaller(true)
}

func main() {
	log.Info("Starting Todolist API server")
	router := mux.NewRouter()
	router.HandleFunc("/healthz", Healthz).Methods("GET")
	http.ListenAndServe(":8000", router)
}
```

In lines 3 to 8, we are importing all the necessary packages we get in our previous  `go get -u` commands.  **Beware that you will get a compilation error when importing any package in Go code without actually using it**.

In lines 10 to 14, we created  `Healthz`  function that will respond  `{"alive": true}`  to the client every time it’s invoked.

In lines 16 to 19, we set our init function to set up our  `logrus`  logger settings. In Golang,  `init()`  will be executed when the program first begins. You can read more [here](https://tutorialedge.net/golang/the-go-init-function/)

In lines 21 to 26, we are initializing our  `gorilla/mux`  HTTP router with a walrus operator. We route  `/healthz`  **HTTP GET**  requests to  `Health()`function. The router will listen to port 8000.

It’s important to set the  `Content-Type: application/json`  so that the client software understands the response.

Simple enough, right? Let’s see what we’ve got so far.

![Healthz endpoint](/blog/7/1-healthz.gif)

Great! Our first endpoint is now live!

## Step 2: Connect to MySQL DB

Before connecting our Go app to the MySQL database, first, we’ll need to have our MySQL server up. We’ll be using Docker in this case to launch a MySQL container and expose the port 3306 to the  _localhost_. We will create a  `todolist`  database in the MySQL container.

```shell
$ docker run -d -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=root mysql  
$ docker exec -it mysql mysql -uroot -proot -e 'CREATE DATABASE todolist'
```

Back to our Go app, we’ll need to install the GORM libraries and dialects.

```shell
$ go get -u github.com/jinzhu/gorm  
$ go get -u github.com/go-sql-driver/mysql  
$ go get -u github.com/jinzhu/gorm/dialects/mysql
```

Add these lines in our  `todolist.go`  code:

```go
package main

import (
	"io"
	"net/http"
	"github.com/gorilla/mux"
	log "github.com/sirupsen/logrus"
+	 _ "github.com/go-sql-driver/mysql"
+	 "github.com/jinzhu/gorm"
+	 _ "github.com/jinzhu/gorm/dialects/mysql"
)

+var db, _ := gorm.Open("mysql", "root:root@/todolist?charset=utf8&parseTime=True&loc=Local")

...

func main() {
	+ defer db.Close()
	log.Info("Starting Todolist API server")
	router := mux.NewRouter()
	router.HandleFunc("/healthz", Healthz).Methods("GET")
	http.ListenAndServe(":8000", router)
}
```

Add our new imports into the import list in lines 8 to 10. In Golang,  **if you do not intend to use the variable, you have to name it**  `_`  . You can read more [here](https://www.geeksforgeeks.org/what-is-blank-identifierunderscore-in-golang/)

Next, we will initialize our MySQL connection to our database using GORM. The following lines are to check if there is an error connecting to the database. If there is, log it.

`defer db.Close()`  means we will close our database connection when our  `main()`  function is returned.

## Step 3: Auto migrate TodoItem ORM Model

GORM comes with a neat database auto-migration feature where it will create a database table based on your struct definition in the code.

Our TodoItem struct will consist of:

-   **Id: int**. This will be our  _primary_key_
-   **Description: string**. This is what we will display in our Frontend UI
-   **Completed: boolean**. This is to determine if the todo item is done or not.

Add these lines to your Go code:

```go
package main

...

var db, _ := gorm.Open("mysql", "root:root@/todolist?charset=utf8&parseTime=True&loc=Local")

+type TodoItemModel struct{
+	 Id int `gorm:"primary_key"`
+	 Description string
+	 Completed bool
+}

...

func main() {
     defer db.Close()
+	 db.Debug().DropTableIfExists(&TodoItemModel{})
+	 db.Debug().AutoMigrate(&TodoItemModel{})
+

	log.Info("Starting Todolist API server")
	router := mux.NewRouter()
	router.HandleFunc("/healthz", Healthz).Methods("GET")
	http.ListenAndServe(":8000", router)
}
```

In lines 7 to 11, we are defining the TodoItem model as what we described earlier. Everything here is self-explanatory.

In lines 19 and 20, we are running Auto-migration against our MySQL database immediately after starting our API server. See it in action here:

![Auto migrations](/blog/7/3-db.gif)

Yeay our database is ready!

## Step 4: Create Todo Item operation

Add the following lines in our `todolist.go`  :

```go
package main

import (
	"io"
	"net/http"
	"github.com/gorilla/mux"
	log "github.com/sirupsen/logrus"
	_ "github.com/go-sql-driver/mysql"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
+	"encoding/json"
)

...

+func CreateItem(w http.ResponseWriter, r *http.Request) {
+	 description := r.FormValue("description")
+	 log.WithFields(log.Fields{"description": description}).Info("Add new TodoItem. Saving to database.")
+	 todo := &TodoItemModel{Description: description, Completed: false}
+	 db.Create(&todo)
+	 result := db.Last(&todo)
+	 w.Header().Set("Content-Type", "application/json")
+	 json.NewEncoder(w).Encode(result.Value)
+}

...
	 log.Info("Starting Todolist API server")
	 router := mux.NewRouter()
  	 router.HandleFunc("/healthz", Healthz).Methods("GET")
+	 router.HandleFunc("/todo", CreateItem).Methods("POST")
     http.ListenAndServe(":8000", router)
}
```

On line 16, we obtain the value from POST operation with  `r.FormValue("parameter")`  . We use the value as the description to insert into our database. After that, we create  `todo`  object and persist it in our database. Lastly, we query the database and return the query result to the client to make sure that the operation is success.

Inline 32, we register the new route  `/todo`  with  **HTTP POST**  request into our new  `CreateItem()`  function.

![Create item endpoint](/blog/7/4-create.gif)

## Step 5: Continue with the rest of CRUD operations

Let’s continue with the rest of Read, Update, and Delete operations.

```go
import (
	"io"
	"net/http"
	"github.com/gorilla/mux"
	log "github.com/sirupsen/logrus"
	_ "github.com/go-sql-driver/mysql"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
	"encoding/json"
+	"strconv"
)

...

+func UpdateItem(w http.ResponseWriter, r *http.Request) {
+	 // Get URL parameter from mux
+	 vars := mux.Vars(r)
+	 id, _ := strconv.Atoi(vars["id"])
+	
+	 // Test if the TodoItem exist in DB
+	 err := GetItemByID(id)
+	 if err == false {
+		 w.Header().Set("Content-Type", "application/json")
+		 io.WriteString(w, `{"updated": false, "error": "Record Not Found"}`)
+	 } else {
+		 completed, _ := strconv.ParseBool(r.FormValue("completed"))
+		 log.WithFields(log.Fields{"Id": id, "Completed": completed}).Info("Updating TodoItem")
+		 todo := &TodoItemModel{}
+		 db.First(&todo, id)
+		 todo.Completed = completed
+		 db.Save(&todo)
+		 w.Header().Set("Content-Type", "application/json")
+		 io.WriteString(w, `{"updated": true}`)
+	 }
+}
+
+func DeleteItem(w http.ResponseWriter, r *http.Request) {
+	 // Get URL parameter from mux
+	 vars := mux.Vars(r)
+	 id, _ := strconv.Atoi(vars["id"])
+
+	 // Test if the TodoItem exist in DB
+	 err := GetItemByID(id)
+	 if err == false {
+		 w.Header().Set("Content-Type", "application/json")
+		 io.WriteString(w, `{"deleted": false, "error": "Record Not Found"}`)
+	 } else {
+		 log.WithFields(log.Fields{"Id": id}).Info("Deleting TodoItem")
+		 todo := &TodoItemModel{}
+		 db.First(&todo, id)
+		 db.Delete(&todo)
+		 w.Header().Set("Content-Type", "application/json")
+		 io.WriteString(w, `{"deleted": true}`)
+	 }
+}
+func GetItemByID(Id int) bool {
+	 todo := &TodoItemModel{}
+	 result := db.First(&todo, Id)
+	 if result.Error != nil{
+		 log.Warn("TodoItem not found in database")
+		 return false
+	 }
+	 return true
+}
+
+func GetCompletedItems(w http.ResponseWriter, r *http.Request) {
+	 log.Info("Get completed TodoItems")
+	 completedTodoItems := GetTodoItems(true)
+	 w.Header().Set("Content-Type", "application/json")
+	 json.NewEncoder(w).Encode(completedTodoItems)
+}
+
+func GetIncompleteItems(w http.ResponseWriter, r *http.Request) {
+	 log.Info("Get Incomplete TodoItems")
+	 IncompleteTodoItems := GetTodoItems(false)
+	 w.Header().Set("Content-Type", "application/json")
+	 json.NewEncoder(w).Encode(IncompleteTodoItems)
+}
+
+func GetTodoItems(completed bool) interface{} {
+	 var todos []TodoItemModel
+	 TodoItems := db.Where("completed = ?", completed).Find(&todos).Value
+	 return TodoItems
}

...

	log.Info("Starting Todolist API server")
	router := mux.NewRouter()
	router.HandleFunc("/healthz", Healthz).Methods("GET")
+	router.HandleFunc("/todo-completed", GetCompletedItems).Methods("GET")
+	router.HandleFunc("/todo-incomplete", GetIncompleteItems).Methods("GET")
	router.HandleFunc("/todo", CreateItem).Methods("POST")
	router.HandleFunc("/todo/{id}", UpdateItem).Methods("POST")
+	router.HandleFunc("/todo/{id}", DeleteItem).Methods("DELETE")
	http.ListenAndServe(":8000", router)
}
```

We should be able to perform Create, Read, Update, and Delete operations on the TodoItems.

In order to update a TodoItem object, we will first query our database to ensure that the item actually exists. I created a function  `GetItemById()`  for this purpose. The equivalent SQL query is  `SELECT * FROM todo_item_models WHERE Id = <Id> LIMIT 1;`  .

Both  `UpdateItem()`  and  `DeleteItem()`  functions will invoke  `GetItemById()`first before actually update or delete them.

Notice that I imported  `strconv`  package in there. The package is used to convert  _String_  variable into  _Integer_  before we run the SQL queries.

The  `GetCompletedItems()`  and  `GetIncompletedItems()`  functions are self-explanatory as well. It will execute SQL Select query and encode them into JSON before returning them to the client.

## Step 6: Connect with Frontend

In order to connect the API server to the frontend page, we need to give the CORS headers to the response. CORS is a mechanism that allows restricted resources on a web page to be requested from another domain outside the domain from which the first resource was served.

To do so, you’ll need to install the go  `cors`  package

```shell
$ go get -u github.com/rs/cors
```

Add these lines to your Go code:

```go
package main

import (
	"io"
	"net/http"
	"github.com/gorilla/mux"
	log "github.com/sirupsen/logrus"
	_ "github.com/go-sql-driver/mysql"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
	"encoding/json"
	"strconv"
	+ "github.com/rs/cors"
)

...

log.Info("Starting Todolist API server")
	router := mux.NewRouter()
	router.HandleFunc("/healthz", Healthz).Methods("GET")
	router.HandleFunc("/todo-completed", GetCompletedItems).Methods("GET")
	router.HandleFunc("/todo-incomplete", GetIncompleteItems).Methods("GET")
	router.HandleFunc("/todo", CreateItem).Methods("POST")
	router.HandleFunc("/todo/{id}", UpdateItem).Methods("POST")
	router.HandleFunc("/todo/{id}", DeleteItem).Methods("DELETE")
	- http.ListenAndServe(":8000", router)
+	
+	 handler := cors.New(cors.Options{
+	 AllowedMethods: []string{"GET", "POST", "DELETE", "PATCH", "OPTIONS"},
+	 }).Handler(router)
+	
+	 http.ListenAndServe(":8000", handler)
}
```

In lines 28 to 32, we’re wrapping CORS handler around our existing application.

Now clone my frontend site for this Go app.

```shell
$ git clone [git@github.com](mailto:git@github.com):sdil/todo.git
```

Then open `index.html` page in your browser. There is nothing special in this simple JQuery page. I have modified the original code made by [themaxsandelin](https://github.com/themaxsandelin/) to execute AJAX requests when you click buttons in the page. Check  `resources/js/main.js`  file to see how the web frontend works.

## Step 7: Build the package

Now we’re done with our coding. You can now compile your code.

```shell
$ go build  
$ ./todolist-mysql-go
```

## Voila!! A little end-to-end demonstration

![End to end demonstration](/blog/7/8-finish.gif)

Congratulations, you’ve built yourself a Todolist in Golang!!

You can find the full source code in my Github repository. If you’re stuck at any point in this tutorial, feel free to refer [there](https://github.com/sdil/learning/blob/master/go/todolist-mysql-go/todolist.go)

## Don’t Stop Learning

This is an, in fact, a tiny part of Golang. There are many more things that you can explore next in Golang. You’ll have more reason to love Golang. I hope this tutorial inspired you to go explore more :D
