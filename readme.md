dynamic-messages
================

A CDI extension to use a dynamically implemented interface for access to your messages in a resource bundle.

## Introduction

This module implements a fancy trick to use localized messages programmatically.

It uses CDI to let the developer:

* create an Interface that defines message-keys by declaring a method
* inject the Interface to wherever this messages should be used
* build a Resource-Bundle (*.properties file) that is automatically resolved and the corresponding message is returned when a method of this Interface is called.

## Usage

### 1. Create a MessageBundle Interface

A message bundle interface is a plain old java interface, tagged with the `@MessageBundle` annotation.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
@MessageBundle
public interface MyMessages {

    /**
     * this is a message declaration by convention.
     *
     * @param who a value for a placeholder in the message, to be used when the message is formatted
     * @return returns the localized greeting message for the current locale
     */
    String greeting(String who);
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

> **NOTE**   
> The signature of the declared message methods should follow these constraints:    
>   
> * must declare a return type, either String, String[] or Object   
> * the name of the method is used as message-key   
> * zero or more parameters may be declared   

### 2. Create a Resource Bundle file

Now, a resource bundle file can be created for each desired locale, as specified in the [ResourceBundle Java Doc](http://docs.oracle.com/javase/7/docs/api/java/util/PropertyResourceBundle.html).

See the example file: `MyMessages_en_US.properties`:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
greeting={0}, welcome!
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As well as the file: `MyMessages_de_CH.properties`:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
greeting=Gruezi {0}!
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

### 3. Use the messages inside a bean

To use the messages in the bean, inject it to a property typed with the interface.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
public class MyBean {

    @Inject
    MyMessages messages;

    // ...

    public String myBusinessMethod() {
        // ... somewhere deep inside the business ...
        String username = ...;
        return messages.greeting(username);
    }
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A call to the `myBusinessMethod()` method will then return a string formatted with the message in the `greeting` property and with the placeholder `{0}` filled by the content of the `username` variable dependent on the current locale.


