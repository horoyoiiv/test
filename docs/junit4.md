JUnit4
=====================


Installation
--------------
dependency를 classpath에 추가  
* **Junit Maven Dependency**  
```
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

* **JUnit Gradle Dependency**  
```
dependencies {
    testImplementation 'junit:junit:4.12'
}
```

Annotation
-------------
|ANNOTATION	|DESCRIPTION|
|------------|-----------|
|@Before |	The annotated method will be run before each test method in the test class.|
|@After	| The annotated method will be run after each test method in the test class.|
|@BeforeClass	| The annotated method will be run before all test methods in the test class. **This method must be static.**|
|@AfterClass	| The annotated method will be run after all test methods in the test class. **This method must be static.**|
|@Test	| It is used to mark a method as junit test|
|@Ignore	| It is used to disable or ignore a test class or method from test suite.|
|@FixMethodOrder	| This class allows the user to choose the order of execution of the methods within a test class.|
|@Rule	| Annotates fields that reference rules or methods that return a rule.|
|@ClassRule	| Annotates static fields that reference rules or methods that return them.|


Write Tests
---------------
```java
import org.junit.*;
public class mytest {

    @BeforeClass
    public static void setup() {
        System.out.println("@BeforeClass once");
    }

    @Before
    public void setupThis() {
        System.out.println("@Before each time");
    }

    @Test
    public void methodA() {
        System.out.println("@Test run test methodA");
        org.junit.Assert.assertTrue(new ArrayList().isEmpty());
    }

    @Test
    public void methodB() {
        System.out.println("@Test run test methodB");
        org.junit.Assert.assertTrue(new ArrayList().isEmpty());
    }


    @After
    public void tearThis() {
        System.out.println("@After each time");
    }

    @AfterClass
    public static void tear() {
        System.out.println("@After once");
    }

}
```
* result  
```
@BeforeClass once
@Before each time
@Test run test methodA
@After each time
@Before each time
@Test run test methodB
@After each time
@After once
```

<br/>

Test Suite
-----------------
* 테스트들을 그룹화하고 한꺼번에 실행시킨다.  

**@RunWith** : 해당 테스트를 **구동,실행**시켜줄 **클래스**를 설정한다.  
>When a class is annotated with @RunWith or extends a class annotated with @RunWith, JUnit will invoke the class it references to run the tests in that class instead of the runner built into JUnit. 

그래서 아래의 RunWith의 인자로 `Suite.class`를 넣어준다.  

**@Suite** : 테스트 하고자 하는 클래스를 나열.  

* mytest와 yourtest 두 개의 test 클래스가 testSuite 클래스 하나로 그룹화되어 실행된다.  
```java
import org.junit.runner.RunWith;
import org.junit.runners.Suite;
import org.junit.runners.Suite.SuiteClasses;

@RunWith(Suite.class)
@SuiteClasses({mytest.class, yourtest.class})
public class testSuite {  
    // blank
}
```

<br/>

JUnitCore
-------------
JUnitCore is a **facade** for running tests.  
```
java.lang.Object
  ㄴ org.junit.runner.JUnitCore
```

* command line에서 테스트 실행
```
java org.junit.runner.JUnitCore testCase1, testCase2
```

<br/>

Executing JUnit testcases with Gradle
-----------------------------------------
command line에서 테스트 실행.
> This is useful for automated build scripts, CI servers.  

```
./gradlew test
gradle test
```
<br/>

Assume
-----------
조건을 만족하지 않는다면 테스트 실패가 아니라 **테스트 skip**이다.
> For example, I want to run a testcase only when some other network service is up. I do not want to fail my test, if service is down.  

* assume을 통과하지 못 하면 해당 테스트를 스킵하는 코드  
```java
    @Test
    public void methodB() {
        String version = "8";
        Assume.assumeTrue(Integer.valueOf(version) == 7);
    }
```
* 결과  
```
unit.mytest > methodB STARTED
unit.mytest > methodB SKIPPED
```

<br/>

Expected Exceptions
--------------------
테스트 실행 시 **예상되는 예외**가 발생하여야 테스트 통과.
```
@Test(expected = NullPointerException.class)
```
* 예외 발생 시 test pass.  
```java
@Test(expected = NullPointerException.class)
public void methodC() {
    String str = null;
    System.out.println(str.length());
}
```

<br/>

Timeout
------------
**milliseconds** 안에 테스트 완료 실패 시 테스트 실패.
```java
    @Test(timeout = 500)
    public void methodD() throws InterruptedException{
        Thread.sleep(1000);
    }
```

* 결과
```
test timed out after 500 milliseconds
org.junit.runners.model.TestTimedOutException: test timed out after 500 milliseconds
```

<br/>

Parameterized Tests With JUnitParams
--------------------------------------
*

```java

@RunWith(Parameterized.class)
public class mytest {

    private final int first;
    private final int second;
    private final int expected;

    @Parameterized.Parameters
    public static Collection<Object[]> data(){
        return Arrays.asList(new Object[][] {
                {1, 1, 1},
                {2, 2, 4},
                {3, 2, 6}
        });
    }


    public mytest(final int first, final int second, final int expected){
        this.first = first;
        this.second = second;
        this.expected = expected;
    }


    @Test
    public void methodA() {
        System.out.println(String.valueOf(first)+" test run");
        Assert.assertEquals(first * second, expected);
    }
    
}
```


