# go 发送 email 邮件

**方法 1:** 使用官方的 net/smtp

```go
package main

import (
	"log"
	"net/smtp"
)

const (
	SMTPHost     = "smtp.gmail.com"
	SMTPPort     = ":587"
	SMTPUsername = "xxx@gmail.com"
	SMTPPassword = "xxxx"
)

func sendEmail(receiver string) {
	auth := smtp.PlainAuth("", SMTPUsername, SMTPPassword, SMTPHost)
	msg := []byte("Subject: 这里是标题内容\r\n\r\n" + "这里是正文内容\r\n")
	err := smtp.SendMail(SMTPHost+SMTPPort, auth, SMTPUsername, []string{receiver}, msg)
	if err != nil {
		log.Fatal("failed to send email:", err)
	}
}

func sendHTMLEmail(receiver string, html []byte) {
	auth := smtp.PlainAuth("", SMTPUsername, SMTPPassword, SMTPHost)
	msg := append([]byte("Subject: 这里是标题内容\r\n"+
		"MIME-version: 1.0;\nContent-Type: text/html; charset=\"UTF-8\";\r\n\r\n"), 
		html...)
	err := smtp.SendMail(SMTPHost+SMTPPort, auth, SMTPUsername, []string{receiver}, msg)
	if err != nil {
		log.Fatal("failed to send email:", err)
	}
}

func main() {
	sendHTMLEmail("接受者@gmail.com", []byte("<html><h2>这是网页内容</h2></html>"))
}
```

**方法 2:** 使用 jordan-wright 库

```go
package main

import (
	"log"
	"fmt"
	"net/smtp"
	"github.com/jordan-wright/email"
)

const (
	SMTPHost     = "smtp.gmail.com"
	SMTPPort     = ":587"
	SMTPUsername = "xxx@gmail.com"
	SMTPPassword = "xxxx"
)

func sendEmail(receiver string) {
	auth := smtp.PlainAuth("", SMTPUsername, SMTPPassword, SMTPHost)
	e := &email.Email{
		From:    fmt.Sprintf("发送者名字<%s>", SMTPUsername),
		To:      []string{receiver},
		Subject: "这里是标题内容",
		Text:    []byte("这里是正文内容"),
	}
	err := e.Send(SMTPHost+SMTPPort, auth)
	if err != nil {
		log.Fatal(err)
	}
}

func sendHTMLEmail(receiver string, html []byte) {
	auth := smtp.PlainAuth("", SMTPUsername, SMTPPassword, SMTPHost)
	e := &email.Email{
		From:    fmt.Sprintf("发送者名字<%s>", SMTPUsername),
		To:      []string{receiver},
		Subject: "这里是标题内容",
		HTML:    html,
	}
	err := e.Send(SMTPHost+SMTPPort, auth)
	if err != nil {
		log.Fatal(err)
	}
}

func main() {
	sendHTMLEmail("接受者@gmail.com", []byte("<html><h2>这是网页内容</h2></html>"))
}
```

**方法 3**: 使用 jordan-wright 库的 Pool

```go
package main

import (
	"log"
	"fmt"
	"time"
	"net/smtp"
	"github.com/jordan-wright/email"
)

const (
	SMTPHost     = "smtp.gmail.com"
	SMTPPort     = ":587"
	SMTPUsername = "xxx@gmail.com"
	SMTPPassword = "xxxx"
	MaxClient    = 5
)

var pool *email.Pool

func sendEmail(receiver string) {
	var err error
	if pool == nil {
		pool, err = email.NewPool(SMTPHost+SMTPPort, MaxClient, smtp.PlainAuth("", SMTPUsername, SMTPPassword, SMTPHost))
		if err != nil {
			log.Fatal(err)
		}
	}
	e := &email.Email{
		From:    fmt.Sprintf("发送者名字<%s>", SMTPUsername),
		To:      []string{receiver},
		Subject: "这里是标题内容",
		Text:    []byte("这里是正文内容"),
	}
	err =  pool.Send(e, 5 * time.Second)
	if err != nil {
		log.Fatal(err)
	}
}
```
**总结:**

如果只是发送少量邮件，可以使用前两种方法。但是如果需要一次性发送较多邮件，需要使用第三种方法，即连接池。