---
layout: post
title: Catch your exceptions like you'd catch a flight; not fish
category: Java
googlewebfonts: Roboto+Mono
---


Imagine you were flying out of New York and you stopped a cab. What would you tell the driver right after you are in the cab?
Will you say "Can you go faster, I have to catch a flight" or will you say "Can you go faster, I have to catch an airport?"

Of course, you would say, "I want to catch a flight", reason? Simply because you have planned for it. 
You know exactly what your destination is you know exactly which airline it is and you know exactly which flight it is. 
You don't get to the airport first and then decide where you want to go and which flight you want to take.

Exceptions in Java are the same, you want to know what exceptions are thrown in advance and be prepared by catching them.

## Java-Exceptions 101
```java
try{    
    //...
    FileInputStream fin=new FileInputStream("mypath.txt");
    while((int data = fin.read())-1){
        
    }    
    //... 
    }catch(FileNotFoundException e){
        //Plan for recovery or log
    }  
```

In above example, `FileNotFoundException` is a checked exception and the code will not compile until it's caught. 
In Java, any exception that does not inherit `java.lang.RuntimeException` or java.lang.Error` is a checked exception.
 
Now let's extend this example

```java
 try (FileInputStream fin = new FileInputStream("mypath.txt")) {
    int data = 0;
    while ((data = fin.read()) != -1) {
        System.out.println(500/data);
    }

    } catch (FileNotFoundException fnf) {
        //plan for failover
    } catch (IOException e) {
        //plan for failover where the file was found but not readable
    }
```

Here, reading the data using `fin` throws another checked exception, IOException, this exception usually means the file was found but the reading of it was interrupted because of some reason.
Now if you were a developer with a foresight you may see that we are dividing an integer with a zero which can throw an exception, but the compiler does not seem to complain about this!
This behavior is because `java.lang.ArithmeticException` is a subclass of `RuntimeException` and as the name suggests it is indeed a Runtime Exception! 
But hey, now you know something can go wrong and you may want to change your code slightly like the following

```java
 try (FileInputStream fin = new FileInputStream("mypath.txt")) {
        int data = 0;
        while ((data = fin.read()) != -1) {
            System.out.println(500/data);
        }
    
    } catch (FileNotFoundException fnf) {
        //plan for failover
    } catch (IOException e) {
        //plan for failover where the file was found but not readable
    } catch (ArithmeticException ae){
        System.out.println("To infinity and beyond");
    }
``` 
 
Now we have also caught `ArithmeticException`, but wait! What about all other exceptions that could be thrown but we don't know about them?

Let's make another attempt at it. First few wrongs and then one right

**WRONG:**

```java
 try (FileInputStream fin = new FileInputStream("mypath.txt")) {
        int data = 0;
        while ((data = fin.read()) != -1) {
            System.out.println(500/data);
        }
    
    } catch (Exception ex) {
        //plan for failover
    }
``` 

**WRONG:**
```java

try (FileInputStream fin = new FileInputStream("mypath.txt")) {
    int data = 0;
    while ((data = fin.read()) != -1) {
        System.out.println(500/data);
    }

} catch (FileNotFoundException fnf) {
    System.out.println("File not found");
} catch (IOException e) {
    System.out.println("Error while reading the file");
} catch (ArithmeticException ae){
    System.out.println("To infinity and beyond");
} catch (Exception ex) {
    System.out.println("Unkown error occurred");
}
``` 

Let's discuss why these changes are not acceptable.

 - The first example catches the superclass, this totally avoids the boring part of catching each exception individually.
  But now this means that the developer did not force himself to think about all the checked exceptions which could have increased the code quality.
  
 - The second example catches all the checked and expected exceptions but in the end, it has `Exception` superclass. Essentially we threw the net too wide. 
 Now even for the future development when someone will write new code no checked exception will be reported because all of them are being caught by the last clause of `try-catch`.
 
 So finally, the right way
 
 **RIGHT:**
 ```java
 try (FileInputStream fin = new FileInputStream("mypath.txt")) {
     int data = 0;
     while ((data = fin.read()) != -1) {
         System.out.println(500/data);
     }
 
 } catch (FileNotFoundException fnf) {
     System.out.println("File not found");
 } catch (IOException e) {
     System.out.println("Error while reading the file");
 } catch (ArithmeticException ae){
     System.out.println("To infinity and beyond");
 } catch (Exception ex) {
     System.out.println("Unkown error occurred");
 }
 ``` 
 
 In the above example, we are catching the RuntimeException which means we are planning for the unexpected but still giving others an opportunity to catch other checked exceptions.
 
 ## Conclusion  
 `Exception` should not be caught until not required by called methods. 

 The same rules also apply to throw exceptions, always throw specific exceptions, never throw `Exception` and finally you don't need to throw `RuntimeException` because all methods in Java throws `RuntimeException` implicitly.
 You can read about it in [https://medium.com/@sapanparikh18/understanding-sonar-squid-redundantthrowsdeclarationcheck-aka-13d86a380411](this) post  
   