# go-lock

### sync.RWMutex

#### 写锁

- Lock
- UnLock

#### 读锁

- RLock
- RUnlock

#### 基本规则

- 读锁与读锁不互斥
- 读锁与写锁互斥
- 写锁与写锁互斥

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	lock := sync.RWMutex{}
	var count int
	wg := sync.WaitGroup{}
	for i := 0; i < 5; i++ {
		wg.Add(1)
		go func(i int) {
			lock.RLock()
			defer lock.RUnlock()
			fmt.Println(i)
			wg.Done()
		}(i)
	}

	for i := 0; i < 3; i++ {
		wg.Add(1)
		go func() {
			lock.Lock()
			defer lock.Unlock()
			fmt.Println("start:", count)
			count++
			fmt.Println("end:", count)
			wg.Done()
		}()
	}
	wg.Wait()
}
```

```
start: 0
end: 1
3
1
0
4
2
start: 1
end: 2
start: 2
end: 3

```

读取内容是乱序，没有互斥

写入内容按照顺序进行，有互斥
