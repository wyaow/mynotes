0. 先举个栗子
A同学做的一个接口，他需要调用数据库的操作。
B同学负责这个接口，但是B经常可能因为抽不出空写这最后一行代码，所以这个接口实现没写完。

在没有Mock的情景下，A有两种方法继续：
A要等到B的接口实现之后才能测试自己的代码。
A写一个调用模拟函数的功能。但是这部分代码后面还是被要删掉。

这个时候就可以使用Mock机制了。

JMockit可以帮你构造一个类，甚至是一个接口，

1. 首先添加相关的POM文件，直接复制即可
<build>
    <plugins>
        <!-- 如果你还需要使用JMockit的代码覆盖率功能，你需要在Maven pom.xml中如下定义 -->
        <plugin>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <argLine>-javaagent:"${settings.localRepository}/org/jmockit/jmockit/1.36/jmockit-1.36.jar=coverage"</argLine>
                <disableXmlReport>false</disableXmlReport>
                <systemPropertyVariables>
                    <coverage-output>html</coverage-output>
                    <coverage-outputDir>${project.build.directory}/codecoverage-output</coverage-outputDir>
                    <coverage-metrics>all</coverage-metrics>
                </systemPropertyVariables>
            </configuration>
        </plugin>
    </plugins>
</build>

<dependencies>
    <!-- 先声明jmockit的依赖, 如果你用mvn test来运行你的测试,那么将jmockit放在junit依赖前面 -->
    <dependency>
        <groupId>org.jmockit</groupId>
        <artifactId>jmockit</artifactId>
        <version>1.36</version>
        <scope>test</scope>
    </dependency>
    <!-- 再声明junit的依赖 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.9</version>
        <scope>test</scope>
    </dependency>
</dependencies>

2. 定义一个类，HelloJokit可以根据Locale这个类实例locale的值确定说中文还是英语.

2.1 我们在测试用例中仍然使用new HelloJMockit, 但是我们构造场景为在中国。


2.2 如果Mock了Locale.class， 这样所有Locale.class的实例都会返回CHINA了。


2.3让他说英语也是一样的



2.3 接下来我们不管Locale，我们直接Mock这个类。为了便于观察，我们模拟这部分代码是别人没实现的。return null


2.3.1 我们Mock HelloJmokit， 让sayhello()返回我们想要的。


2.3.2 Mock作用域降低到函数内部。


2.3.3 你还可以使用MockUp， 这个也是mock的类或者接在函数内生效。



下面这三种，第一种整个文件内的都Mock了。对于很多重复的可以使用。
第二种，第三种就范围小点。个人觉得第三种挺好排版的。
@Mocked
HelloJMockit helloJMockit;
public void test2(final @Mocked HelloJMockit helloJMockit /* 这是一个测试参数 */) {
new MockUp<Calendar>(Calendar.class) {
    // 想Mock哪个方法，就给哪个方法加上@Mock， 没有@Mock的方法，不受影响
    @Mock
    public int get(int unit) {

3. 当然你也可以直接写一个类，继承MockUp<T>, 其原理是一样的，可以看官网
