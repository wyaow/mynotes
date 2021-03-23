
使用一览:
1.采集数据
1.1对应带server的程序，导入相应包
1.2对于带server的程序，导入相应包

2.分析数据
2.1在线数据分析
2.2离线数据分析（将别的机器产生的pprof文件放到其他机器分析，本质是一样的，不过这个对于Linux服务器，我们拿出pprof文件到开发机器上分析更方便）

 
1.导入包，启动监听。
1.1带server的应用
import(
"log"
"net/http"
_"net/http/pprof"
)

func main(){
go func(){
log.Println(http.ListenAndServe("localhost:6060",nil))
}()
}
 

1.2不带server的应用
import(
"flag"
"runtime/pprof"
)

var cpuprofile=flag.String("cpuprofile","","writecpuprofiletofile")

func main(){
//其他代码...
//往指定文件写入
pprof.StartCPUProfile(f)
defer pprof.StopCPUProfile()
//其他代码...
}
然后启动服务即完成第一步。

 
2.可以获取下面多种类型的profile文件
1. heap profile
go tool pprof http://localhost:6060/debug/pprof/heap
 

2. cpu profile
go tool pprof http://localhost:6060/debug/pprof/profile?seconds=30
 

3. block profile
go tool pprof http://localhost:6060/debug/pprof/block

4.execution trace(5s)
wget http://localhost:6060/debug/pprof/trace?seconds=5

 

5.mutex
go tool pprof http://localhost:6060/debug/pprof/mutex
也可以用浏览器打开, 在上面点点点

http://localhost:6060/debug/pprof/

 

# 主要命令
# 下载cpu profile，默认从当前开始收集30s的cpu使用情况，需要等待30s

go tool pprof http://47.93.238.9:8080/debug/pprof/profile
# wait 120s

go tool pprof http://47.93.238.9:8080/debug/pprof/profile?seconds=120

# 下载 heap profile
go tool pprof http://47.93.238.9:8080/debug/pprof/heap

# 下载 goroutine profile
go tool pprof http://47.93.238.9:8080/debug/pprof/goroutine

# 下载 block profile
go tool pprof http://47.93.238.9:8080/debug/pprof/block

# 下载 mutex profile
go tool pprof http://47.93.238.9:8080/debug/pprof/mutex


3.安装可视化工具graphviz，安装之后（将bin目录加至环境变量path下）看图效果好。
<<graphviz-2.38-win32.msi>>


4.分析

4.1执行2中的任意一个命令，之后会生成一个pprof相应类型文件。

go tool pprof http://localhost:6060/debug/pprof/profile?seconds=30


计算机生成了可选文字:
D八GOSrc\loopdevice＼。ain>90toolpprofhttp://localhost:606。／debu月／pprof/profile?seconds=3
FetchingprofileoverH,I''I'Pfromhttp://localhost:6O6e/debu只／pprof/pro+ile户seconds=3
SavedprofileinIc:\Users\*3的。6e46\pProf\pprof.saoples.cpu.OeS.Pb．月z}
Type:cpu
Time:Aug3,2020at4:39pm(cST)
Duration:35,Totalsamples=e
NOsamples树erefoundwiththedefaultsaoplevaluetype.
Try'"saople_index"co而andtoanalyzedifferentsaoplevalues.
Enteringinteractivemode(type"help"forco一ands,"o"foroptions)
(pprof)
(pprof)
(pprof)l

 

4.2可以直接在里面输命令top,list,web （我喜欢4.3用ui看）

计算机生成了可选文字:
(pprof)tops
Showingnodesaccountingfore,e%ofe
flatflat%sum笼
CUm
CU
tot己1
袱
(pprof)
(pprof)
(pprof)
树eb
列名
含义
flat
本函数的执行耗时
flat%
flat 占 CPU 总时间的比例。程序总耗时 16.22s, Eat 的 16.19s 占了 99.82%
sum%
前面每一行的 flat 占比总和

cum
累计量。指该函数加上该函数调用的函数总耗时
cum%
cum 占 CPU 总时间的比例



4.3 在web上分析文件
go tool pprof -http :8090 C:\Users\w30006046\pprof\pprof.samples.cpu.006.pb.gz
 

5.分析远程服务器数据

获取远程数据如下

在本机浏览器访问，点击下载对应pprof文件

计算机生成了可选文字:
O/debug/pprofjx+
令令co不安全｝,0．炭汉袅袋．3.：务／debug/PProf/
/debtlg/pProf/
Typesofprofilesavailable:
COUntPF0file
352al!OCS
ob!OCk
0Cmd!ine
569皿四五丝
352垃坦p
0mUteX
O匹区业
32th「eadCreate
0tFaCe
1』上g区四血旦丛匹丘卫坦卫p

 

计算机生成了可选文字:
盆
profile
洲气

接下来按4.3分析。

 

总结：

总共分三步：

1.go代码导入pprof相应包，安装可视化工具

2.采集数据（开发 或者 服务器）

3.分析数据（手输或者ui看）

“过早的优化是万恶之源”。实际工作中，很少有人会关注性能，但当你写出的程序存在性能瓶颈，qa 压测时，qps 上不去，为了展示一下技术实力，还是要通过 pprof 观察性能瓶颈，进行相应的性能优化。

 

参考资料

https://golang.org/pkg/net/http/pprof/

https://lrita.github.io/2017/05/26/golang-memory-pprof/#7-%E4%BD%BF%E7%94%A8syncpool%E6%9D%A5%E7%BC%93%E5%AD%98%E5%B8%B8%E7%94%A8%E7%9A%84%E5%AF%B9%E8%B1%A1

https://segmentfault.com/a/1190000016412013

https://www.cnblogs.com/qcrao-2018/p/11832732.html


