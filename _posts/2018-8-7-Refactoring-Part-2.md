---
layout: post
title: Refactoring Part 2
category: Refactoring
googlewebfonts: Roboto+Mono
---
### REPLACE TYPE CODE WITH SUBCLASSES
As you start working with more complex system  you will find yourself end up with classes which has type-code and then logic around it. Like the following example

```java
public class Appointment{
    //.....
    private int type;
    public static final int VIRTUAL_CONFERENCE = 0;
    public static final int FACETOFACE = 1;
    public static final int TELE_CONFERENCE = 2;
    //.....
    public Appointment(int type){
        this.type = type;
    }
    public String getInformation(int type, Date dateTime){
        switch (type){
            case VIRTUAL_CONFERENCE:
                return conferenceRoom +"\n" + conferenceNum + "\n" + conferenceTime.toString();
            case FACETOFACE:
                return conferenceRoom + "\n" + conferenceTime.toString();
            case TELE_CONFERENCE:
                return conferenceRoom + "\n" + conferenceTime.toString(); +"\n" +conferenceTele + "\n" + conferenceCode;
        }
                
    }
    
}

//In this case the client code would look like the following
public void createAppointments(){
    //...
    Appointment appointment = new Appointment(Appointment.VIRTUAL_CONFERENCE,now);
    Appointment appointment2 = new Appointment(Appointment.FACETOFACE,today);
    Appointment appointment3 = new Appointment(2,tomorrow);
    printAppointmentInformation(appointment);
    printAppointmentInformation(appointment2);
    printAppointmentInformation(appointment3);
    
}
```

This does seem like a great case where we can convert type code to subclass. Here you can also establish that Virtual_Conference IS AN appointment!
Have we just found an opportunity to use inheritance? Let's try it.

Assuming once an appointment is created it can not be converted to other type, we can directly use subclassing.

```java
abstract class Appointment{
    private ConferenceTime conferenceTime;
    abstract public String getInformation();   
    
}

class FaceToFaceAppointment extends Appointment{
    //...
    @override
    public String getInformation(){
        return conferenceRoom + "\n" + conferenceTime.toString();
    }
}

class VirtualAppointment extends Appointment{
     //...
        @override
        public String getInformation(){
            return conferenceRoom +"\n" + conferenceNum + "\n" + conferenceTime.toString();
        }
}

class TeleAppointment extends Appointment{
     //...
        @override
        public String getInformation(){
            return conferenceRoom + "\n" + conferenceTime.toString(); +"\n" +conferenceTele + "\n" + conferenceCode;
        }
}

//Now rather than depending on switch statements our clients can use the following code
public void createAppointments(){
    //...
    Appointment appointment = new FaceToFaceAppointment(now);
    Appointment appointment2 = new TeleAppointment(today);
    Appointment appointment3 = new VirtualAppointment(tomorrow);
    printAppointmentInformation(appointment);
    printAppointmentInformation(appointment2);
    printAppointmentInformation(appointment3);
    
}
public void printAppointmentInformation(Appointment appt){
    System.out.println( appointment.getInformation());
}
``` 
Finally if you look at this the code is more resistant against bugs because in normal scenario when you use integer or other type codes they are still just regular integers for rest of the system.
So for an instance, even though you may be using `Appointment.VIRTUAL_CONFERENCE` in your code, for the method being called it is still a simple integer. 
Which means there is no compile time check if you call your constructor with invalid argument like 
```java 
Appointment appt = new Appointment(1000000,datetime)
```