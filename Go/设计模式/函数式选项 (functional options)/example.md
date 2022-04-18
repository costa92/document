# 函数式选择模式



```go
package main

import "fmt"

type User struct {
	ID     string
	Name   string
	Age    int
	Email  string
	Phone  string
	Gender string
}

type UserOption func(*User)

func WithAge(age int) UserOption {
	return func(u *User) {
		u.Age = age
	}
}

func WithEmail(email string) UserOption {
	return func(u *User) {
		u.Email = email
	}
}

func WithPhone(phone string) UserOption {
	return func(u *User) {
		u.Phone = phone
	}
}

func WithGender(gender string) UserOption {
	return func(u *User) {
		u.Gender = gender
	}
}

func NewUser(id string, name string, options ...UserOption) *User {
	user := User{
		ID:     id,
		Name:   name,
		Age:    0,
		Email:  "",
		Phone:  "",
		Gender: "",
	}
	for _, option := range options {
		option(&user)
	}
	return &user
}

func main() {
	user := NewUser("23233", "cist", WithAge(111))
	fmt.Println(user)
}
```

