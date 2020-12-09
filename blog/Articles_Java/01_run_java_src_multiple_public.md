### Running a java source with multiple public class files

So there is a funny behaviour around JDK 11 when we create a java source file with multiple public classes in it.
Consider the following example:

```java
//File name : MultiplePublicClass.java
public class AnotherPublicClass { 

  public void x(){
    System.out.println("Hello From AnotherPublicClass.x ");
  }

  public static void x1(){
    System.out.println("Hello From AnotherPublicClass.x1 ");
  }
  public static void main(String ... args){
    System.out.println("Hello From AnotherPublicClass.main ");
  }
}

public class MultiplePublicClassTest{

  public static void main(String ... args){
    System.out.println("Hello From MultiplePublicClassTest.main ");
    AnotherPublicClass apc = new AnotherPublicClass();
    apc.x();
    AnotherPublicClass.x1();
  }
}
```

Try Compiling it with `javac` :  

```dos
> javac MultiplePublicClass.java
```

It will fail with following error.

```dos

MultiplePublicClassTest.java:1: error: class AnotherPublicClass is public, should be declared in a file named AnotherPublicClass.java
public class AnotherPublicClass { 
       ^
1 error
```

But wait for a moment here. How about we try to use new feature of Java 11 and try running the java source directly instead of 
compiling which writes class file to disk. 
Its done like this :

```
> java MultiplePublicClass.java
```

When we run like this the java launcher will _compile_ the source in memory and will launch the **First `main()` method**
it encounters irrespective of whether the first class in which the `main()` method is encountered is `public` or `default` access scoped.

It will produce the following output :
```
Hello From AnotherPublicClass.main 
```
If you change the class order - `MultiplePublicClassTest` comes first and `AnotherPublicClass` comes second in the source file and you repeat the process it will now execute the `main()` method available inside the `MultiplePublicClassTest` class.
Following will be the output:
``` 
Hello From MultiplePublicClassTest.main 
Hello From AnotherPublicClass.x 
Hello From AnotherPublicClass.x1 
```

#### Summary  

* If both classes above are public. Compiler will give error and it will fail.  
    ```
    class AnotherPublicClass is public, should be declared in a file named 
    AnotherPublicClass.java
    ```
It wont compile to `.class` file unless there is either no public class
or there is only 1 public class and its name is same as the file name.

* Different behaviour of java launcher:
    * java launcher `java.exe` running in source mode can execute first main method
    encountered in the file Irrespective of the file name and access specifier of the internal classes.
    It will execute the main method of 1st class it encounters top to bottom.

* So the overall observation is that if we put two public classes in the file,
the `.java` file will not compile but it can be executed by java launcher in source mode.  

<script src="../utterances.js"
</script>
