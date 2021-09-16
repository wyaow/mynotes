go有三种类型的函数，单元测试函数、基准测试函数和示例函数
类型	格式	作用
测试函数	函数名前缀为Test	测试程序的一些逻辑行为是否正确
基准函数	函数名前缀为Benchmark	测试函数的性能
示例函数	函数名前缀为Example	为文档提供示例文档
go help test
go test [-c] [-i] [build flags] [packages] [flags for test binary]
参数解读：

-c : 编译 go test 成为可执行的二进制文件，但是不运行测试。
-i : 安装测试包依赖的 package，但是不运行测试。
关于 build flags，调用 go help build，这些是编译运行过程中需要使用到的参数，一般设置为空
关于 packages，调用 go help packages，这些是关于包的管理，一般设置为空
关于 flags for test binary，调用 go help flags for test binary，这些是 go test 过程中经常使用到的参数

常用示例如下：
-test.v : 是否输出全部的单元测试用例（不管成功或者失败），默认没有加上，所以只输出失败的单元测试用例。
-test.run pattern: 只跑哪些单元测试用例
-test.bench patten: 只跑那些性能测试用例
-test.benchmem : 是否在性能测试的时候输出内存情况
-test.benchtime t : 性能测试运行的时间，默认是1s
-test.cpuprofile cpu.out : 是否输出cpu性能分析文件
-test.memprofile mem.out : 是否输出内存性能分析文件
-test.blockprofile block.out : 是否输出内部goroutine阻塞的性能分析文件
-test.memprofilerate n : 内存性能分析的时候有一个分配了多少的时候才打点记录的问题。这个参数就是设置打点的内存分配间隔，也就是 profile 中一个 sample 代表的内存大小。默认是设置为 512 * 1024 的。如果将它设置为 1，则每分配一个内存块就会在 profile 中有个打点，那么生成的 profile 的 sample 就会非常多。如果设置为0，那就是不做打点了。可以通过设置 memprofilerate=1 和 GOGC=off 来关闭内存回收，并且对每个内存块的分配进行观察。
-test.blockprofilerate n: 基本同上，控制的是 goroutine 阻塞时候打点的纳秒数。默认不设置就相当于 -test.blockprofilerate=1，每一纳秒都打点记录一下
-test.parallel n : 性能测试的程序并行 cpu 数，默认等于 GOMAXPROCS。
-test.timeout t : 如果测试用例运行时间超过t，则抛出 panic
-test.cpu 1,2,4 : 程序运行在哪些 CPU 上面，使用二进制的1所在位代表，和 nginx 的 nginx_worker_cpu_affinity 是一个道理
-test.short : 将那些运行时间较长的测试用例运行时间缩短

#### 编译成可执行文件之后的命令
go test -c -o tt ./...
./tt -run TestFunc
flag provided but not defined: -run
Usage of ./tt:
  -test.bench regexp
    	run only benchmarks matching regexp
  -test.benchmem
    	print memory allocations for benchmarks
  -test.benchtime d
    	run each benchmark for duration d (default 1s)
  -test.blockprofile file
    	write a goroutine blocking profile to file
  -test.blockprofilerate rate
    	set blocking profile rate (see runtime.SetBlockProfileRate) (default 1)
  -test.count n
    	run tests and benchmarks n times (default 1)
  -test.coverprofile file
    	write a coverage profile to file
  -test.cpu list
    	comma-separated list of cpu counts to run each test with
  -test.cpuprofile file
    	write a cpu profile to file
  -test.failfast
    	do not start new tests after the first test failure
  -test.list regexp
    	list tests, examples, and benchmarks matching regexp then exit
  -test.memprofile file
    	write an allocation profile to file
  -test.memprofilerate rate
    	set memory allocation profiling rate (see runtime.MemProfileRate)
  -test.mutexprofile string
    	write a mutex contention profile to the named file after execution
  -test.mutexprofilefraction int
    	if >= 0, calls runtime.SetMutexProfileFraction() (default 1)
  -test.outputdir dir
    	write profiles to dir
  -test.parallel n
    	run at most n tests in parallel (default 4)
  -test.run regexp
    	run only tests and examples matching regexp
  -test.short
    	run smaller test suite to save time
  -test.testlogfile file
    	write test action log to file (for use only by cmd/go)
  -test.timeout d
    	panic test binary after duration d (default 0, timeout disabled)
  -test.trace file
    	write an execution trace to file
  -test.v
    	verbose: print additional output

### go test
-run 执行指定函数
-v 显示细节

这里介绍几个常用的参数：
-bench regexp 执行相应的 benchmarks，例如 -bench=.；
-cover 开启测试覆盖率；
-run regexp 只运行 regexp 匹配的函数，例如 -run=Array 那么就执行包含有 Array 开头的函数；
-v 显示测试的详细命令。


#### 执行指定测试用例
go test helloworld_test.go
go test -run TestFunc1 helloworld_test.go
go test -run TestFunc1 -v ./..

#### 标记单元测试结果
当需要终止当前测试用例时，可以使用 FailNow，参考下面的代码。
测试结果标记（具体位置是./src/chapter11/gotest/fail_test.go）
func TestFailNow(t *testing.T) {
    t.FailNow()
    或者
    t.Fail()
}

#### 单元测试日志
每个测试用例可能并发执行，使用 testing.T 提供的日志输出可以保证日志跟随这个测试上下文一起打印输出。
testing.T 提供了几种日志输出方法，详见下表所示。
单元测试框架提供的日志方法
方法备注
Log	打印日志，同时结束测试
Logf	格式化打印日志，同时结束测试
Error	打印错误日志，同时结束测试
Errorf	格式化打印错误日志，同时结束测试
Fatal	打印致命日志，同时结束测试
Fatalf	格式化打印致命日志，同时结束测试
开发者可以根据实际需要选择合适的日志。


#### 基准测试——获得代码内存占用和运行效率的性能数据
基准测试可以测试一段程序的运行性能及耗费CPU的程度。Go语言中提供了基准测试框架，使用方法类似于单元测试，
使用者无须准备高精度的计时器和各种分析工具，基准测试本身即可以打印出非常标准的测试报告。
1) 基础测试基本使用
下面通过一个例子来了解基准测试的基本使用方法。

基准测试（具体位置是./src/chapter11/gotest/benchmark_test.go）
package code11_3
import "testing"
func Benchmark_Add(b *testing.B) {
    var n int
    for i := 0; i < b.N; i++ {
        n++
    }
}
这段代码使用基准测试框架测试加法性能。第 7 行中的 b.N 由基准测试框架提供。测试代码需要保证函数可重入性及无状态，
也就是说，测试代码不使用全局变量等带有记忆性质的数据结构。避免多次运行同一段代码时的环境不一致，不能假设 N 值范围。

##### 行开启基准测试：
$ go test -v -bench=. benchmark_test.go
goos: linux
goarch: amd64
Benchmark_Add-4           20000000         0.33 ns/op
PASS
ok          command-line-arguments        0.700s
代码说明如下：
第 1 行的-bench=.表示运行 benchmark_test.go 文件里的所有基准测试，和单元测试中的-run类似。
第 4 行中显示基准测试名称，2000000000 表示测试的次数，也就是 testing.B 结构中提供给程序使用的 N。“0.33 ns/op”表示每一个操作耗费多少时间（纳秒）。
##### 注意：Windows 下使用 go test 命令行时，-bench=.应写为-bench="."。

#### 基准测试原理
基准测试框架对一个测试用例的默认测试时间是 1 秒。开始测试时，当以 Benchmark 开头的基准测试用例函数返回时还不到 1 秒，那么 testing.B 中的 N 值将按 1、2、5、10、20、50……递增，同时以递增后的值重新调用基准测试用例函数。
#### 自定义测试时间
通过-benchtime参数可以自定义测试时间，例如：
$ go test -v -bench=. -benchtime=5s benchmark_test.go
goos: linux
goarch: amd64
Benchmark_Add-4           10000000000                 0.33 ns/op
PASS
ok          command-line-arguments        3.380s
#### 测试内存
基准测试可以对一段代码可能存在的内存分配进行统计，下面是一段使用字符串格式化的函数，内部会进行一些分配操作。
func Benchmark_Alloc(b *testing.B) {
    for i := 0; i < b.N; i++ {
        fmt.Sprintf("%d", i)
    }
}

#### 在命令行中添加-benchmem参数以显示内存分配情况，参见下面的指令：
$ go test -v -bench=Alloc -benchmem benchmark_test.go
goos: linux
goarch: amd64
Benchmark_Alloc-4 20000000 109 ns/op 16 B/op 2 allocs/op
PASS
ok          command-line-arguments        2.311s
代码说明如下：
第 1 行的代码中-bench后添加了 Alloc，指定只测试 Benchmark_Alloc() 函数。
第 4 行代码的“16 B/op”表示每一次调用需要分配 16 个字节，“2 allocs/op”表示每一次调用有两次分配。

开发者根据这些信息可以迅速找到可能的分配点，进行优化和调整。
5) 控制计时器
有些测试需要一定的启动和初始化时间，如果从 Benchmark() 函数开始计时会很大程度上影响测试结果的精准性。testing.B 提供了一系列的方法可以方便地控制计时器，从而让计时器只在需要的区间进行测试。我们通过下面的代码来了解计时器的控制。

基准测试中的计时器控制（具体位置是./src/chapter11/gotest/benchmark_test.go）：
func Benchmark_Add_TimerControl(b *testing.B) {
    // 重置计时器
    b.ResetTimer()
    // 停止计时器
    b.StopTimer()
    // 开始计时器
    b.StartTimer()
    var n int
    for i := 0; i < b.N; i++ {
        n++
    }
}
从 Benchmark() 函数开始，Timer 就开始计数。StopTimer() 可以停止这个计数过程，做一些耗时的操作，通过 StartTimer() 重新开始计时。ResetTimer() 可以重置计数器的数据。
计数器内部不仅包含耗时数据，还包括内存分配的数据。


#### 测试覆盖率 go test -cover
测试覆盖率是代码被测试套件覆盖的百分比。通常使用的都是语句的覆盖率，也就是在测试中至少被运行一次的代码占总代码的比例。

GO 提供内置功能来检查代码覆盖率。可以使用 go test -cover 来查看测试覆盖率。例如:5
$ go test -cover 

PASS
coverage: 100.0% of statements
ok      algorithm/split 0.005s
可以看到测试用例的代码覆盖率是 100%。

Go 还提供了额外的 -coverprofile 参数，用来将覆盖率相关的记录信息输出到一个文件：
go test -cover -coverprofile=c.out

PASS
coverage: 100.0% of statements
ok      algorithm/split 0.005s
上面的命令会将覆盖率相关的信息输出到当前文件夹下面的 c.out 文件中，然后执行 go tool cover -html=c.out，使用 cover 工具来处理生成的记录信息，该命令会打开本地的浏览器窗口生成一个 HTML 报告:


#### 基准测试
go test -bench=Split
go test -bench=Split -benchmem  获得内存分配的统计数据
go test -bench=.
go test -bench=Fib40 -benchtime=20s

综合pprof
go test -bench=. -benchmem -cpuprofile profile.out
go test -bench=. -benchmem -memprofile memprofile.out -cpuprofile profile.out // 还可以同时查看内存

这会在当前目录下生成 memprofile.out 和 profile.out 文件，接下来可以用输出的文件使用 pprof：
go tool pprof profile.out 
然后也可以用 list 命令检查函数需要的时间:
(pprof) list Fib
还可以通过 web 命令生成图像

####Setup 与 TearDown
测试程序有时需要在测试之前进行额外的设置（setup）或在测试之后进行拆卸（teardown）
// 测试集的 Setup 和 Teardown
func setupTestCase(t *testing.T) func(t *testing.T) {
	t.Log("如有需要哦在此执行：测试之前的 setup")
	return func(t *testing.T) {
		t.Log("如有需要在此执行：测试之后的 teardown")
	}
}

// 子测试的 Setup 和 Teardown
func setupSubTest(t *testing.T) func(t *testing.T) {
	t.Log("如有需要在此执行：子测试之前的 setup")
	return func(t *testing.T) {
		t.Log("如有需要在此执行：子测试之后的 teardown")
	}
}

func TestSplit(t *testing.T) {
	type test struct { // 定义test结构体
		input string
		sep   string
		want  []string
	}
	tests := map[string]test{ // 测试用例使用map存储
		"simple":      {input: "a:b:c", sep: ":", want: []string{"a", "b", "c"}},
		"wrong sep":   {input: "a:b:c", sep: ",", want: []string{"a:b:c"}},
		"more sep":    {input: "abcd", sep: "bc", want: []string{"a", "d"}},
		"leading sep": {input: "枯藤老树昏鸦", sep: "老", want: []string{"", "枯藤", "树昏鸦"}},
	}
	teardownTestCase := setupTestCase(t) // 测试之前执行 setup 操作
	defer teardownTestCase(t)            // 测试之后执行 teardown 操作

	for name, tc := range tests {
		t.Run(name, func(t *testing.T) { // 使用 t.Run() 执行子测试
			teardownSubTest := setupSubTest(t) // 子测试之前执行 setup 操作
			defer teardownSubTest(t)           // 测试之后执行 teardown 操作
			got := Split(tc.input, tc.sep)
			if !reflect.DeepEqual(got, tc.want) {
				t.Errorf("excepted:%#v, got:%#v", tc.want, got)
			}
		})
	}
}


