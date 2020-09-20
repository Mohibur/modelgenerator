# Model Generator For MySQL with GOLang


## comand

```sh
## generate model files from model.json
mysqlmodel process model.json

## to generate model file under template-dest directory described in model.json, application will ask for hostname, username, password and database  
mysqlmodel model employee model.json --dsn "username:password@tcp(hostname)/databasename?additional=param"

```

## model.json 

```json
{
  "template-src": "company-profile/mysql-template/",
  "template-dest": "company-profile/app/models/",
  "mysql-golbal-object": "app.DB",
  "package-name": "models"
}
```
__note__: This is expected that `compny-profile` created under `${GOPATH}`
__note__: mysql-golbal-object will be used to generate query command, such as `app.DB.Query("SELECT * FROM ")`



## under path `/home/god/gopath/src/company-profile/mysql-template/` file `employee.json`

```json
{
  "filename": "employee",
  "imports": ["simple/app"],
  "struct": [
    {
      "name": "id",
      "type": "int",
      "isId": true
    },
    {
      "name": "name",
      "type": "string"
    },
    {
      "name": "city",
      "type": "string"
    }
  ],
  "enable": ["fetchall", "insert", "update", "count"],
  "selectBy": [["id"], ["name", "city"], ["city"]],
  "extended": "employee.ext"
}

```
## under path `/home/god/gopath/src/company-profile/mysql-template/` file `employee.exe`

```go
func GetPartialMatchByName(namePart string) Employee {
  app.DB.Query("...")
  ...
  return emp
}

```

## after executing `mysqlmodel process model.json`

```go
package models

import (
	"simple/app"
	"database/sql"
)

type Employee struct {
	id   int
	name string
	city string
}

func (e Employee) GetId() int {
	return e.id
}

func (e Employee) SetId(id int) {
	e.id = id
}

func (e Employee) GetName() string {
	return e.name
}

func (e Employee) SetName(name string) {
	e.name = name
}

func (e Employee) GetCity() string {
	return e.city
}

func (e Employee) SetCity(city string) {
	e.city = city
}


func transform(rows *sql.Rows) []Employee {
	employee := Employee{}
	res := []employee{}
	for rows.Next() {
		var id int
		var name string
		var city string
		err := rows.Scan(&id, &name, &city)
		if err != nil {
			panic(err.Error())
		}

		emp.id = id
		emp.name = name
		emp.city = city
		res = append(res, emp)
	}
	return res
}

func (_ Employee) FetchAll() []Employee {
	rows, err := app.DB.Query("SELECT id, name, city from employee")
	if err != nil {
		return nil
	}

	defer rows.Close()
	toRet := transform(rows)
	return toRet
}

func Insert() Employee {
	rows, err := app.DB.Query("SELECT id, name, city from employee")
	if err != nil {
		return nil
	}

	defer rows.Close()
	toRet := transform(rows)
	return toRet
}

##  TODO UNDER CONSTRUCTION

```
