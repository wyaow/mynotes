基准测试。


1.POM配置

1.1添加依赖

<!-- 基准测试JMH -->
<dependency>
    <groupId>org.openjdk.jmh</groupId>
    <artifactId>jmh-core</artifactId>
</dependency>
<dependency>
    <groupId>org.openjdk.jmh</groupId>
    <artifactId>jmh-generator-annprocess</artifactId>
</dependency>
1.2. 注意：如果要用IDE进行benchmark测试，POM还要记得配置这个

<!-- 使用jmh的话,还需要添加这个 -->
<configuration>
  <transformers >
    <mainClass>com.***...main</mainClass>
    <!-- 使用jmh的话,还需要添加这个 -->
    <mainClass>org.openjdk.jmh.Main</mainClass>
  </transformers>
</configuration>

2. 基准测试代码，其实很简单。复制看一遍就会。

import org.openjdk.jmh.annotations.*;
import org.openjdk.jmh.runner.Runner;
import org.openjdk.jmh.runner.RunnerException;
import org.openjdk.jmh.runner.options.Options;
import org.openjdk.jmh.runner.options.OptionsBuilder;

import java.util.LinkedList;
import java.util.List;
import java.util.concurrent.TimeUnit;

/**
 * Description: JMH基准测试
 * 执行基准测试有两种形式
 * 1.生成jar文件的形式主要是针对一些比较大的测试，可能对机器性能或者真实环境模拟有一些需求，需要将测试方法写好了放在linux环境执行。具体命令如下
 * mvn clean install
 * java -jar target/benchmarks.jar
 * 2.直接在IDE中跑就好
 *
 **/
 
 @State(Scope.Benchmark)
@OutputTimeUnit(TimeUnit.SECONDS)
@Fork(value = 1)
@Threads(Threads.MAX)
// @Threads(1)
// Threads.MAX
public class ListBenchMarkTest {
    private static final int SIZE = 10000;

    private List<String> list = new LinkedList<>();

    @Setup
    public void setUp() {
        for (int i = 0; i < SIZE; i++) {
            list.add(String.valueOf(i));
        }
    }

    @Benchmark
    @BenchmarkMode(Mode.Throughput)
    public void forIndexIterate() {
        for (int i = 0; i < list.size(); i++) {
            list.get(i);
            System.out.print("");
        }
    }

    @Benchmark
    @BenchmarkMode(Mode.Throughput)
    public void forEachIterate() {
        for (String s : list) {
            System.out.print("");
        }
    }

    /**
     * 这是benchmark 启动的入口, 这里同时还完成了JMH测试的一些配置工作
     * 默认场景下，JMH会去找寻标注了@Benchmark的方法，可通过include和exclude两个方法来完成包含以及排除的语义
     */
    public static void main(String[] args) throws RunnerException {
        Options opt = new OptionsBuilder()
                // 可以整个类的所有方法,也可以单独方法;也可以用XXX.class.getSimpleName()
                .include(ListBenchMarkTest.class.getSimpleName())
                //.include("grpcUploadFile")
                //.exclude("grpcUploadFile")
                // 预热10轮
                // .warmupIterations(10)
                // 代表正式计量测试做10轮，而每次都是先执行完预热再执行正式计量，
                // .measurementIterations(2)
                //  forks(3)指的是做3轮测试，因为一次测试无法有效的代表结果，
                .forks(1)
                // .output("D:/Benchmark.log")
                .build();
        new Runner(opt).run();
    }
}

3.运行测试，点击main函数运行。就可以看美妙操作次数。


4. 这两个可以记下，就是基准测试前的初始化，和后的还原操作。before和after是jmock的写法。

// @Before  // 这个是JMock的用法
@Setup
public void setUp() {
    campMessageServiceUtilMockUp = new MockUp<CampMessageServiceUtil>() {
    };
    campUtil = campMessageServiceUtilMockUp.getMockInstance();
    campUtil.init();
}
// @After   // 这个是JMock的用法
@TearDown
public void unsetUp() {
    campUtil.closeManagedChannel();
}

5.其他的关键字用法可以参考下面的两篇文章。


参考文章

https://hg.openjdk.java.net/code-tools/jmh/file/2be2df7dbaf8/jmh-samples/src/main/java/org/openjdk/jmh/samples
https://www.jianshu.com/p/f106e92b52ce
 
