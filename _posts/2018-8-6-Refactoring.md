---
layout: post
title: Refactoring Part-1
category: refactoring
googlewebfonts: Roboto+Mono
---

These are some of my notes I took while reading Martin Fowler's _Refactoring. Improving THE Design of Existing Code_.

While refactoring is synonymous to “code clean up” before you start cleaning a legacy system a good set of test cases is a prerequisite. Having a suite of test cases will keep two things in check.

1. While you refactor code you are not introducing new bugs 
2. As a result of refactoring, you are not making your system slower

While you start refactoring the system a slight performance loss is expected but the assumption is that only 10% of the code is the root cause behind the slowness of any system and refactoring will help you identify that 10% as opposed to speculating what’s slow and what’s not.

## CODE IDENTIFICATION
Usually you would want to start with a method or class which is irrationally long and complex. In today’s tools, the most suitable code fragments will show up with high cyclomatic or cognitive complexity. Or other simpler yardstick to identify code that needs refactoring is simply find all methods which are too long, maybe more than 50 to 100 lines.  I have seen a system containing single method bodies spanning more than 10000 lines. That being said there is no easy way to define “long methods”. Maybe you can start with the top 10% of your longest methods!

## REFACTORING TECHNIQUES 
Following are a few refactoring methodologies you can use while working on a large legacy system.



### EXTRACT METHOD
This refactoring method is the easiest to identify and perform. If in your code you are able to find a piece of code that changes only one variable then it may be best to move it to its own method and return the value of that variable. The variables which are not being changed but are part of the logic should be sent as parameters. Usual targets to extracting methods are logical code blocks like if, else, for loop etc. This way not only you are shortening the method but also decreasing the cognitive complexity of the overall method. Essentially, in the end, your code will be easier to understand and modify.
```java
public class 
```

### MOVE METHOD
You can use Move Method refactoring techniques in one of the following scenarios.
If your code depends too much on data of another class then most probably your method should also be in another class. This means if you are using far too many getters and setters of other class before a logic is executed then the logic should be moved to the class which is being instantiated to invoke getters and setters.
If you are working on a legacy system which does not have a separate data access layer or a proper MVC then you may want to move chunks of code accessing database to DAO. In a nutshell, follow an architecture, move your DB access code to DAO, move your UI related code to UI components, and move your logic to services.

### INTRODUCE PARAMETER OBJECT
Methods with many primitive parameters are the best candidates to introduce parameter objects. In many cases one can observe a few parameters exists together, a most common example is a date range. If you have methods which are expecting parameters like `fromDate` and `toDate` it may be a good idea to move them to a class named DateRange with fields fromDate, toDate. Not only number of parameters will decrease in your method they will be easily understandable to a reader of the code. Also, one more crucial benefit is that you get a central place for validation of inputs. Imagine, rather than just passing dates to method what if you could instantiate a DateRange object where the constructor also took the duration for validation and threw an exception if date range was more than the duration passed in the constructor!

### INTRODUCE FOREIGN METHOD
There will be cases when you are working with library classes when the class does not have the methods you need. A great example is Java's `Date` class. 
Developers do find themselves writing date formatting or date arithmetic related code repeatedly. If the class you are using needs only one or two methods and you can not change the class itself then you may want to create a method that does what you need.


### INTRODUCE LOCAL EXTENSION
There may be a scenario where a library class does not have several methods you need. In such a case a new class should be created by either extension or delegation which includes the methods you need. 


### ENCAPSULATE COLLECTIONS
Collections should be handled differently if you are using Lists, Sets, Maps etc. You should not have getters or setters for the entire collection. If you do, then the outside classes will be able to change, nullify, elements. 
Also, returning entire collections exposes information about what may be going inside your class. So in cases like following.
```java
public class Currency{
    Map <String,String>symbolMap = new HashMap<>();
    public Currency(){
        symbolMap.put("USD","$");
        symbolMap.put("EUR","€");
        symbolMap.put("GBP","£");
        symbolMap.put("INR","₹");
        symbolMap.put("JPY","¥");
        
    }
    
    public Map<String,String> getSymbolMap(){
        return symbolMap;
    }
}

public class Client{
    String code;
    String symbol;
    public  CurrencyClient(String code){
        this.symbol = new Currency().getSymbolMap().get(code);
        // Here I can do
        new Currency().getSymbolMap().clear(); //you just gave someone access to your inner workings!
    }
}


```

This should be rewritten as

```java
public class Currency{
    Map <String,String>symbolMap = new HashMap<>();
    public Currency(){
        symbolMap.put("USD","$");
        symbolMap.put("EUR","€");
        symbolMap.put("GBP","£");
        symbolMap.put("INR","₹");
        symbolMap.put("JPY","¥");
        
    }
    
    public String getSymbol(String symbol){
        return symbolMap.get(symbol);
    }
}

public class Client{
    String code;
    String symbol;
    public  CurrencyClient(String code){
        this.symbol = new Currency().getSymbol(code);
        this.code = code;
        
    }
}


```  
 
### REPLACE INHERITANCE WITH DELEGATION
One of the core concepts of OOP design, inheritance, while it is really great when you want to reuse code it should be used carefully.  
Inheritance has a special meaning, the class that extends a class becomes one of them! For instance, if Manager class inherits Person then you can safely say "A manager is a person".
Also, now Manager will automatically have all the methods like `getFullName()` `getAddress()` etc. 

All this being said, sometimes you may see yourself inhering a class which does not share "IS A" relationship with the child. 
For example, your DAO may inherit DBConnection class to use utility methods like `close()`, `getConnection()` etc. like the following

```java
public class EmployerDAO extends  DBConnection{
    
    public void saveEmployer(Employer e){
        //..
        Connection connection = getConnection(); //in this case getConnection is an inherited method.
        //..
    }
    
}
``` 

But if you look closely at the above example and ask yourself, "Is EmployerDAO a DBConnection" and you are most certainly going to tell yourself "NO!".

These are the cases when you change your inheritance to design to rather use delegation. Like the following.

```java
public class EmployerDAO {
    DBConnection dbConnection = new DBConnection();
    public void saveEmployer(Employer e){
        //..
        Connection connection = dbConnection.getConnection(); //in this case responsibility to connect to a db is delegated
        //..
    }
    
}
``` 

  




