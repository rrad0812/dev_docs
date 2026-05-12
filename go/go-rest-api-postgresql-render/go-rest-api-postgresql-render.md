
# Building and Deploying a REST API with Golang, Postgres, and Render.com

## 1 Basic setup

In this tutorial, we will walk through the process of building a simple REST API using the Go programming language, following the Clean Architecture principles. Our API will use PostgreSQL as the database, and we will deploy it on render.com. To achieve this, we will leverage the following dependencies:

- Gin: An HTTP web framework
- Sqlx: A database adapter
- Envconfig: A tool for managing environment variables
- Wire: A dependency injection builder

### 1.1 Database setup

Let’s start by setting up the PostgreSQL database. You have two options: running PostgreSQL in a Docker container or installing it directly on your computer. If you choose Docker, execute the following command to create a container:

```sh
docker run --name my-postgres -e POSTGRES_PASSWORD=password -p 5432:5432 -d postgres
```

Here’s what the command flags do:

- **--name my-postgres**: Assigns the name "my-postgres" to the container.
- **-e POSTGRES_PASSWORD=password**: Sets the PostgreSQL password.
- **-p 5432:5432**: Maps the host's port 5432 to the container's port 5432 for database access.
- **-d**: Runs the container in detached mode.

Once the PostgreSQL container is running, create the database, table, and insert a sample record by opening a terminal session and executing the following commands:

```sh
docker exec -it my-postgres psql -U postgres -W
```

Inside the PostgreSQL prompt:

```sql
create database todo_db_development;
\c todo_db_development
CREATE TABLE todos (
    id SERIAL PRIMARY KEY,
    body VARCHAR(255) NOT NULL,
    completed BOOLEAN NOT NULL DEFAULT false
);
INSERT INTO todos (body) VALUES ('Learn golang');
```

### 1.2 Setting Up Dependencies

Let’s proceed to set up our Go application and install the necessary dependencies. Start by initializing a Go module and installing the required packages:

```sh
go mod init github.com/<your_repository>/todo-api
go get github.com/lib/pq
go get github.com/jmoiron/sqlx
go get github.com/gin-gonic/gin
go get github.com/kelseyhightower/envconfig
go get github.com/google/wire
```

### 1.3 Prepare environment variables

Create a "local.env" file to manage environment variables for our application:

```sh
touch local.env
echo 'DSN="postgres://postgres:password@127.0.0.1/todo_db_development?sslmode=disable"' >> local.env
echo 'GIN_PORT=":7070"' >> local.env
```

Note: the DSN variable is based on your postgres configuration.

### 1.4 Building the Main Application

Next, create "the main.go" file to initialize our application and test the PostgreSQL connection:

```go
// cmd/api/main.go
package main

import (
    "fmt"
    "log"

    "github.com/jmoiron/sqlx"  
    "github.com/kelseyhightower/envconfig"
    _ "github.com/lib/pq"
)

type Config struct {
    Dsn     string `required:"true"`
    GinPort string `required:"true" split_words:"true"`
}

// LoadConfig učitva konfiguraciju Config iz env. varijabli
// U slučaju greške vraća nil i omotanu grešku
func LoadConfig() (*Config, error) {
    var c Config
    err := envconfig.Process("", &c)
    if err != nil {
        return nil, fmt.Errorf("failed to load config: %w", err)
    }
    return &c, nil
}

// NewPostgreaDb kreira konekciju
// Vraća sqlx.Db objekat ili grešku ako je ima
func NewPostgresDB(conf *Config) (*sqlx.DB, error) {
    db, err := sqlx.Connect("postgres", conf.Dsn)
    if err != nil {
        return nil, err
    }
    return db, nil
}

func main() {
    conf, err := LoadConfig()
    if err != nil {
        log.Fatalln(err)
    }

    log.Println("configurations: ", conf)
    db, err := NewPostgresDB(conf)
    if err != nil {
        log.Fatalln(err)
    }

    log.Println("connected to db with: ", db)
}
```

### 1.5 Running the Application

Finally, run the main application to test the PostgreSQL connection:

```sh
export $(cat local.env | xargs)
go run cmd/api/main.go
```

If everything is set up correctly, your terminal should display output similar to the following:

```sh
2023/08/10 11:09:42 configurations:  &{postgres://postgres:password@127.0.0.1/todo_db_development?sslmode=disable :7070}
2023/08/10 11:09:42 connected to db with:  &{0xc000100d00 postgres false 0xc0000789f0}
```

## 2 Build app

Welcome back to the second part of our tutorial! In previous part, we set up very basic application to connect with postgreSQL.

Here, we’ll create the core parts of our app: the repository, use case, and handler. These layers help us handle data, logic, and user requests.

### 2.1 Application design overview

- **Repository**: This layer interacts directly with the database, handling data storage and retrieval.
- **Service**: The service layer houses the core business logic, orchestrating how data flows between different components.
- **Handler**: Responsible for managing incoming HTTP requests from users via their browsers, this layer plays a pivotal role in connecting the user interface to the application logic.

Now, let’s get down to work and start building the backbone of our application.

### 2.2 Defining Data Access and Business Logic

We’ll begin by setting up basic stuff. We’ll make files and define simple rules. Don’t worry, it’s easy!

Open a new file named todo.go inside the internal/domain folder. Here's what we'll put in it:

```go
// internal/domain/todo.go
package domain

import "context"

type TodoID int

type Todo struct {
    ID        TodoID `db:"id" json:"id"`
    Body      string `db:"body" json:"body"`
    Completed bool   `db:"completed" json:"completed"`
}

type TodoRepository interface {
    Select(ctx context.Context) ([]*Todo, error)
    Insert(ctx context.Context, body string) (*Todo, error)
}

type TodoUsecase interface {
    List(ctx context.Context) ([]*Todo, error)
    Create(ctx context.Context, body string) (*Todo, error)
}
```

- Todo struct: This defines how we store a todo. We describe how it appears in the database (db), and how it's presented as data when we interact with the app (json).
- TodoRepository interface: We lay out two tasks here: getting todos and adding todos
- TodoUsecase interface: This is how people see todos and create new ones.

### 2.3 Repository implementation

We’ll create a repository that interacts with the database and handles READ, CREATE operations. Here’s the code snippet:

```go
// internal/repository/todo_repository.go
package repository

import (
    "context"
   
    "github.com/hienvl125/todo-api/internal/domain"
    "github.com/jmoiron/sqlx"
)

type todoRepository struct {
    db *sqlx.DB
}

func NewTodoRepository(db *sqlx.DB) domain.TodoRepository {
    return &todoRepository{db: db}
}

func (r todoRepository) Select(ctx context.Context) ([]*domain.Todo, error) {
    todos := []*domain.Todo{}
    if err := r.db.Select(&todos, "SELECT * FROM todos ORDER BY id DESC"); err != nil {
        return nil, err
    }

    return todos, nil
}

func (r todoRepository) Insert(ctx context.Context, body string) (*domain.Todo, error) {
    lastInsertId := 0
    if err := r.db.
        QueryRow("INSERT INTO todos(body) VALUES($1) RETURNING id", body).
        Scan(&lastInsertId); err != nil {
        return nil, err
    }

    var todo domain.Todo
    if err := r.db.Get(&todo, "SELECT * FROM todos WHERE id = $1", lastInsertId); err != nil {
        return nil, err
    }
    
    return &todo, nil
}
```

Note: You might wonder why we use the `RETURNING id` clause in the INSERT statement. PostgreSQL doesn’t natively support returning IDs, so we manually retrieve and scan the ID into a variable to ensure we capture the ID of the newly inserted todo.

### 2.4 Usecase Implementation

The usecase layer handles business logic and uses the repository. Here’s the code:

```go
// internal/service/todo_service.go
package service

import (
    "context"
   
    "github.com/hienvl125/todo-api/internal/domain"
)

type todoService struct {
    todoRepository domain.TodoRepository
}

func NewTodoService(todoRepository domain.TodoRepository) domain.TodoUsecase {
    return &todoService{todoRepository: todoRepository}
}

func (s todoService) List(ctx context.Context) ([]*domain.Todo, error) {
    todos, err := s.todoRepository.Select(ctx)
    if err != nil {
        return nil, err
    }
   
    return todos, nil
}

func (s todoService) Create(ctx context.Context, body string) (*domain.Todo, error) {
    todo, err := s.todoRepository.Insert(ctx, body)
    if err != nil {
        return nil, err
    }
   
    return todo, nil
}
```

Lastly, the handler layer handles HTTP requests and uses the usecase layer.

### 2.5 Handler Implementation

```go
// internal/handler/todo_handler.go
package handler

import (
    "net/http"
   
    "github.com/gin-gonic/gin"
    "github.com/hienvl125/todo-api/internal/domain"
)

type TodoHandler struct {
    todoService domain.TodoUsecase
}

func NewTodoHandler(todoService domain.TodoUsecase) *TodoHandler {
    return &TodoHandler{todoService: todoService}
}

func (h TodoHandler) List(ctx *gin.Context) {
    todos, err := h.todoService.List(ctx)
    if err != nil {
        ctx.AbortWithError(http.StatusInternalServerError, err)
        return
    }
   
    ctx.JSON(http.StatusOK, gin.H{
        "todos": todos,
    })
}

func (h TodoHandler) Create(ctx *gin.Context) {
    var todoInput domain.Todo
    if err := ctx.BindJSON(&todoInput); err != nil {
        ctx.AbortWithError(http.StatusBadRequest, err)
        return
    }
    if todoInput.Body == "" {
        ctx.AbortWithStatusJSON(http.StatusBadRequest, gin.H{
            "message": "body can't be blank",
        })
        return
    }
    
    todo, err := h.todoService.Create(ctx, todoInput.Body)
    if err != nil {
        ctx.AbortWithError(http.StatusInternalServerError, err)
        return
    }
    
    ctx.JSON(http.StatusOK, gin.H{
        "todo": todo,
    })
}
```

You might notice we use different methods like ctx.AbortWithError, ctx.AbortWithStatusJSON, and ctx.JSON. Here's why:

- **ctx.AbortWithError**: This logs internal errors without exposing them outside.
- **ctx.AbortWithStatusJSON**: Used for validation errors that are okay to show to users.
- **ctx.JSON**: Responds with a JSON-format HTTP response.

### 2.6 Setup routes

Let’s craft a function to establish our handlers(routes) for our application. This function will play a pivotal role in defining the behavior of our API.

```go
// internal/handler/handler.go
package handler

import "github.com/gin-gonic/gin"

func SetupHandlers(todoHandler *TodoHandler,) *gin.Engine {
    router := gin.Default()
   
    todoRouterGroup := router.Group("/todos")
    todoRouterGroup.GET("/", todoHandler.List)
    todoRouterGroup.POST("/", todoHandler.Create)
   
    return router
}
```

### 2.7 Connect everything

In part 1, we created the main.go file to establish and test our database connection. Now, let's take the next step and enhance our application by integrating the Gin framework to set up an HTTP server and test its functionality.

```go
// cmd/api/main.go
package main
...

func main() {
  ...
  todoRepository := repository.NewTodoRepository(db)
  todoService := service.NewTodoService(todoRepository)
  todoHandler := handler.NewTodoHandler(todoService)
  ginServer := handler.SetupHandlers(todoHandler)
  if err := ginServer.Run(conf.GinPort); err != nil {
    log.Fatalln(err)
  }
}
```

Let’s start the server and put our APIs to the test!

```sh
export $(cat local.env | xargs)
go run cmd/api/main.go
```

Creating a New Todo:

```sh
curl -X POST 'localhost:7070/todos' \
 -H 'Content-Type: application/json' \
 -d '{
 "body": "Create a new todo"
}'
```

After running this command, you should receive a response like:

```sh
{"todo":{"id":18,"body":"Create a new todo","completed":false}}
```

Listing Todos:

```sh
curl --location 'localhost:7070/todos'
```

You’ll get a response similar to:

```sh
{"todos":[{"id":18,"body":"Create a new todo","completed":false}]}
```

### 2.7 Dependency injections with Wire

As we journey forward in crafting our RestAPI, let’s introduce you to the magic of google/wire for handling dependencies. This ingenious tool offers a simpler way compared to manual dependency setup.

we’ll take a few steps to neatly organize our code:

1. Separating configurations:

   ```go
   // internal/util/config/config.go
   package config
   
   import (
       "fmt"
      
       "github.com/kelseyhightower/envconfig"
   )
   
   type Config struct {
       Dsn     string `required:"true"`
       GinPort string `required:"true" split_words:"true"`
   }
   
   func LoadConfig() (*Config, error) {
       var c Config
       err := envconfig.Process("", &c)
       if err != nil {
           return nil, fmt.Errorf("failed to load config: %w", err)
       }
      
       return &c, nil
   }
   ```

2. Separating database connection:

   ```go
   // internal/util/db/db.go
   package db
   
   import (
       "github.com/hienvl125/todo-api/internal/util/config"
       "github.com/jmoiron/sqlx"
       _ "github.com/lib/pq"
   )
   
   func NewPostgresDB(conf *config.Config) (*sqlx.DB, error) {
       db, err := sqlx.Connect("postgres", conf.Dsn)
       if err != nil {
           return nil, err
       }
           return db, nil
   }
   ```

3. Prepare the registry

   ```go
   // internal/registry/wire.go
   // +build wireinject
   
   package registry
   
   import (
       "github.com/gin-gonic/gin"
       "github.com/google/wire"
       "github.com/hienvl125/todo-api/internal/handler"
       "github.com/hienvl125/todo-api/internal/repository"
       "github.com/hienvl125/todo-api/internal/service"
       "github.com/hienvl125/todo-api/internal/util/config"
       "github.com/hienvl125/todo-api/internal/util/db"
   )
   
   func NewGinServer(conf *config.Config) (*gin.Engine, error) {
       wire.Build(
           handler.SetupHandlers,
           handler.NewTodoHandler,
           service.NewTodoService,
           repository.NewTodoRepository,
           db.NewPostgresDB,
       )
   
       return nil, nil
   }
   ```

   Note: +build wireinject directive to indicate files that are used for the code generation process.

4. Generating Dependencies:

   With a simple command, generate dependency injections based on the registry:

   ```sh
   wire internal/registry/wire.go
   ```

   Note: If you cannot use above command, just install wire tool.

   ```sh
   go install github.com/google/wire/cmd/wire@latest
   ```

5. Modify cmd/api/main.go

```go
// cmd/api/main.go
package main

import (
    "log"
   
    "github.com/hienvl125/todo-api/internal/registry"
    "github.com/hienvl125/todo-api/internal/util/config"
)

func main() {
    conf, err := config.LoadConfig()
    if err != nil {
        log.Fatalln(err)
    }
   
    ginServer, err := registry.NewGinServer(conf)
    if err != nil {
        log.Fatalln(err)
    }
   
    if err := ginServer.Run(conf.GinPort); err != nil {
        log.Fatalln(err)
    }
}
```

## 3 Render

Welcome to the third part of our tutorial series! In previous part, we created a basic REST API Go app using clean architecture principles.

In this section, I’ll show you how to create a PostgreSQL instance and a web server instance on **render.com**, and then link them together.

Source code of this part is available on this branch.

### 3.1 Creating a PostgreSQL Instance

Setting up a database instance on render.com is a breeze. Let’s walk through the process step by step:

- First, visit this link <https://dashboard.render.com/new/database>
  ![01](images/01.webp)  
- Next, choose the instance type. For my example, I’ll go with the **free** option.
  ![02](images/02.webp)  
- After creating the database, "render.com" will take you to the instance details page. Here,
  you’ll find two types of connections:
  - Internal Database URL: This is useful for render.com’s services to communicate securely on
    their private network, enhancing the security of your instances.
  - External Connection: If you need to connect to render.com’s PostgreSQL instance from an
    external database tool like DBeaver or Sequel Pro, you’ll want to use this option.  
    **Note**: Be sure to copy the internal database URL and keep it handy; we’ll need it later.
    ![03](images/03.webp)  
- Now, switch to the External Connection tab and copy the “PSQL Command.” We’ll use this command to connect to the database from our terminal and set up some basics.
  ![04](images/04.webp)  
- Paste the copied command from render.com into your terminal. Once connected to the database
  instance, use the following commands (one by one) to create a table and insert some sample records:

The result will be look like this if running them successfully

![05](images/05.webp)  

**Describe tables**:

```sh
\dt
```

**Create todos table**:

```sql
CREATE TABLE todos (
    id SERIAL PRIMARY KEY,
    body VARCHAR(255) NOT NULL,
    completed BOOLEAN NOT NULL DEFAULT false
);
```

**Describe tables again to confirm created table**:

```sh
\dt
```

**Create sample records**:

```sh
INSERT INTO todos (body) VALUES ('Learn golang');
INSERT INTO todos (body) VALUES ('Learn postgresql');
```

**Retrive records from todos table**:

```sh
SELECT * FROM todos;
```

**To quit psql**:

```sh
\q
```

### 3.2 Creating a web server instance

Set up a database instance, and now we’ll make a web server instance to put our Go app on render.com. Go to this link: <https://dashboard.render.com/create?type=web> and pick the first choice since we’re deploying from a GitHub repository. After that, we’ll connect our repository to this instance. I’ll use the “Public Git Repository” option.

![06](images/06.webp)  

Next, provide some essential details for your instance. Choose the branch that has the application code you want to deploy.

![07](images/07.webp)  

Afterwards, scroll down and set up your “Build Command” and “Start Command.”

![08](images/08.webp)  

- go build -o bin/api ./cmd/api/main.go: Create an executable binary named api from `cmd/api/main.go` in the `bin` directory for running our Go app.
- ./bin/api: A command to run the executable file named `api` located in the `bin` directory.

Then, select your instance type. Once again, I’ll go with the “Free” option.

![09](images/09.webp)  

Next, click on “Advanced” to access advanced settings, where we’ll add the required environment variables.

We’ll need to set up environment variables for our Go app.

- GIN_PORT: should be set to the port number that the Go app uses.
- DSN: Please use the Internal Database Connection that we saved earlier.

  ![10](images/10.webp)  

Scroll down to the bottom and submit these configurations.

After submitting the creation of a new web instance, Render.com will direct you to the detailed page of the newly created instance. Simply wait a few minutes, and your web server will be up and running.

![11](images/11.png)  

Let’s try to access it.

![12](images/12.webp)  

Please be aware that from now on, whenever a new commit is made to the branch you have chosen, Render.com will apply the latest code from that branch and trigger a redeployment.

### 3.3 Download the Medium app

After submitting the creation of a new web instance, Render.com will direct you to the detailed page of the newly created instance. Simply wait a few minutes, and your web server will be up and running.

Let’s try to access it.

Please be aware that from now on, whenever a new commit is made to the branch you have chosen, Render.com will apply the latest code from that branch and trigger a redeployment.
