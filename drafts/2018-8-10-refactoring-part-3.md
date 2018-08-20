### FORM TEMPLATE METHOD (PATTERN)
### INLINE TO TEMP
### SPLIT TEMP VARIABLE
### REMOVE ASSIGNMENTS TO PARAMETERS
### EXTRACT CLASS
### HIDE A DELEGATE 
Instead of `employee.getDepartment().getManager()` it should be `employee.getManager()`

### INTRODUCE A FOREIGN METHOD
What we call helper methods. Used to work with library classes to which we do not have access to edit code.
### Introduce local extension
When a literary class does not provide methods that you frequently need you may want to create a new class that solely extends functionalities of literary class. A good example would be a class that adds functionalities to Date in Java.
### Replace data value with object
In a simpler world, we start with representing programming concepts with  data types like Strings and integers.  But in some cases you will realize a simple string does not suffice all the logic we may want, for example, a string that represents full name may need it’s own method to render names. Ex: “firstName LastName”, or “Last, fullName” or names with title etc. 

### REPLACE ARRAY WITH OBJECT 
### REPLACE MAGIC NUMBER WITH CONSTANT 

### REPLACE TYPE CODE WITH SUBCLASSES
If you have a class where final type codes exist, for example, in a class named “Appointment” if you have a “private int appointmentType;” which can have values associated with “face to face”, “conf call” etc then you may want to create a superclass called appointment and create subclasses of it like ConferenceCall, and FaceToFaceAppointment which extends Appointment class. 





