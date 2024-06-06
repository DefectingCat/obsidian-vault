# 并发计算

Go 使用 goroutine 来实现多线程。goroutine 是绿色线程，是由 go runtime 来管理的一种多线程，并非为 1：1 的系统线程。