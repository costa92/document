# Redis client Mock

## Install 

```sh
go get github.com/go-redis/redismock/v9
```

## explian
```go

package redis_test

import (
	"context"
	"errors"
	"fmt"
	"testing"
	"time"

	"github.com/go-redis/redismock/v9"
	"github.com/redis/go-redis/v9"
)

var ctx = context.TODO()

func NewsInfoForCache(redisDB *redis.Client, newsID int) (info string, err error) {
	cacheKey := fmt.Sprintf("news_redis_cache_%d", newsID)
	info, err = redisDB.Get(ctx, cacheKey).Result()
	if err == redis.Nil {
		// info, err = call api()
		info = "test"
		err = redisDB.Set(ctx, cacheKey, info, 30*time.Minute).Err()
	}
	return
}

func TestNewsInfoForCache(t *testing.T) {
	db, mock := redismock.NewClientMock()

	newsID := 123456789
	key := fmt.Sprintf("news_redis_cache_%d", newsID)

	// mock ignoring `call api()`

	mock.ExpectGet(key).RedisNil()
	mock.Regexp().ExpectSet(key, `[a-z]+`, 30*time.Minute).SetErr(errors.New("FAIL"))

	_, err := NewsInfoForCache(db, newsID)
	if err == nil || err.Error() != "FAIL" {
		t.Error("wrong error")
	}

	if err := mock.ExpectationsWereMet(); err != nil {
		t.Error(err)
	}
} 

```

```sh
go test -v


