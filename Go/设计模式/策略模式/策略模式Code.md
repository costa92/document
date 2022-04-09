# 策略模式 code

```go
import (
	"fmt"
	"io/ioutil"
	"os"
)

type StorageStrategy interface {
	Save(name string, data []byte) error
}

var strategys = map[string]StorageStrategy{
	"file":         &fileStorage{},
	"encrypt_file": &encryptFileStorage{},
}

func NewStorageStrategy(t string) (StorageStrategy, error) {
	s, ok := strategys[t]
	if !ok {
		return nil, fmt.Errorf("not found StorageStrategy:%s", t)
	}
	return s, nil
}

type fileStorage struct {
}

func (s *fileStorage) Save(name string, data []byte) error {
	return ioutil.WriteFile(name, data, os.ModeAppend)
}

type encryptFileStorage struct{}

func (s *encryptFileStorage) Save(name string, data []byte) error {
	data, err := encrypt(data)
	if err != nil {
		return err
	}
	return ioutil.WriteFile(name, data, os.ModeAppend)
}

func encrypt(data []byte) ([]byte, error) {
	return data, nil
}


```

测试：
```go
import (
	"github.com/stretchr/testify/assert"
	"testing"
)

func Test_demo(t *testing.T)  {
	data,sensitive := getData()
	strategysType := "file"
	if sensitive {
		strategysType = "encrypt_file"
	}
	storage,err := NewStorageStrategy(strategysType)
	assert.NoError(t, err)
	assert.NoError(t, storage.Save("test.txt",data))
}

func getData()([]byte,bool)  {
	return []byte("test data 222"),true
}
```

