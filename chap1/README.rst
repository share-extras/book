Dashlets, Dashlets, everywhere
==============================

Dashlets are often the first experience people have of developing on top of Share.

As we'll explore in the following chapters they are actually only one of many ways in which new functions can be added to the application, but they they offer a powerful yet simple extension mechanism which makes them well suited as a starting point for this book.

Central to understanding Dashlets are Web Scripts, which were discussed in the previous chapter. Before we move on to looking in more detail at a dashlet, you should be familiar with what capabilities web scripts provide and how they are implemented.

We will discuss how Dashlets are implemented shortly, but first lets's consider the key characteristics of a Dashlet.

1. Specific to either a particular user or a particular Share site
2. May make use of data loaded from the Repository or from other external systems
3. May be associated with configuration attributes affecting their behaviour
4. Able to encapsulate appropriate business logic for the purposes of displaying data
5. Return a human-readable representation of that data in HTML format
(Diagram showing inputs and outputs)

In this chapter we will consider a range of example Dashlets which illustrate the wide range of capabilities they may provide and the many ways in which they may be implemented, but all the examples will share these central features.

An understanding of dashboards in general and their relevance in modern applications is outside of the scope of this book, but you should understand that every site and every user in Alfresco has a dashboard associated with them, and that each dashboard defines the set of Dashlets which appears on it in addition to its physical position.

(Picture showing example)

Finally you should be aware that multiple instances of any given dashlet may reside on a single dashboard.

(Picture showing example)

Dashlets and Page components
---------------------------

Another way of thinking of dashlets is as dynamically-generated series of page components. When we discuss adding custom page components and overriding existing components in the later chapters, you may wish to return to this definition of a dashlet to fit it into the wider context.

What a dashlet is not - should not be overly complex
Now, with these ideas in mind we can begin to look at how we can build Dashlets ourselves.

Implementation of a dashlet
--------------------------

Dashlets reside within the web-tier in the Alfresco application model. In addition the repository-tier provides persistence of their state and configuration, however you do not need to worry about this as the framework calls the necessary services automatically. You just need to implement the standard pattern.

At its core a dashlet is simply a single web-tier web script, identified within one of the following three web script families, and which provides a HTML representation as its default output.

- site-dashlet Always associated with a specific site
- user-dashlet Always associated with a specific user
- dashlet May be associated with a specific site or a specific user

(Venn diagram) 

The markup output by the dashlet will generally contain two distinct blocks - a title and a body.

(diagram)

More advanced dashlets may define additional blocks for toolbars and such like, and most practical dashlets will utilise some client-side code called using inline scripts, but we will consider such cases later in this chapter.

My first dashlet
---------------

To illustrate this most basic type of dashlet we will start off by looking at a basic dashlet which simply prints a static title and body text.

This will serve as a recap to you if you have implemented web scripts before, and will introduce the type of markup required for dashlets specifically, which we have described already.

If you are not familiar with developing on Share you will first want to consult Chapter 1 to ensure you have a suitable lightweight development environment set up. This example and those which follow will assume that you are comfortable editing XML, HTML and other types of source files in a text editor and with deploying those into a running Alfresco instance for testing.

Example 1
~~~~~~~~~

Adding configuration
-------------

Example 2
~~~~~~~~~

Adding behaviour (controller)
-------------

In the last example example we saw how using some basic Freemarker conditional expressions it is possible to add some intelligent behaviour to our dashlet. However he designers of Freemarker deliberately provide only a basic set of expressions for performing logic, since it is intended to be used as a view on information which has already been processed (see Model-View-Controller design pattern in REF).

To get around this problem and add more complex behaviours we must add a controller element to our dashlet web script. It is possible to define behaviours in Java or JavaScript, but we will concentrate on the latter throughout this book to simplify our implementation.

Example 3
~~~~~~~~~

Adding client-side behaviour
------------------------------

You may already be familiar with adding client-side behaviour, if for example, you have added JavaScript to a web page in the past. Just like modern web pages use JavaScript code to speed up initial loading time and provide a richer user experience, Share also uses such an approach within the dashlets and other page components that we will encounter in this book.

Since Share is a complex application in itself, the client-side JavaScript code is more structured than one would normally encounter on a simple web page. All code should be appropriately namespaced, and although static, global functions are frequently used, the bulk of processing logic should be abstracted into re-usable objects using a typical object-oriented approach.

We will see how this is done in the examples which follow, but for now we just need to make a distinctions between the two main types of objects that will be used.

Components are normally specific to a certain dashlet or page component implementation and will normally only be instantiated once on any instance of that component. When you write your own custom client-side JavaScript, you will nearly always be implementing components.

Modules provide commonly-used capabilities which are better implemented as objects rather that as static methods. You may frequently use Alfresco-supplied modules in implementing your own components, but it is rarer to implement custom modules yourself.

Using client-side modules - Adding a resizer

This is one of the simplest modules that you can use to add client-side behaviour to your dashlets. It allows users to change the vertical height of an individual dashlet instance, which is then persisted to the dashlet's configuration.

* Any user may change the height of a user dashlet on their own dashboard
* Site Managers may change the height of a site dashlet

We will discuss more on dashlet configuration later on, but for now we'll just assume that the height is supplied to the dashlet web script as the property args.height, but noting that it may not be set if the dashlet has not been customised.

Example 4
~~~~~~~~~

First show FTL markup, then show JS controller changes needed. Also include 4.0 method.

Adding Title Bar Actions
---------------

The second most common module you will use in your dashlets is the title bar actions. This module allows the developer to add one or more clickable actions on the right hand side of the dashlet title bar.

Formerly in Alfresco 3, such actions, for example to display a dialogue panel, would have required adding a separate toolbar to the dashlet. Although toolbars are still used in some circumstances, simple actions can be added more unobtrusively via this client-side module.

IMAGE

Another change in Alfresco 4 was the help action provided in all Alfresco core dashlets. This allows the user to click a help icon to pop up a small amount of help text intended to explain to them how to use the dashlet. It is recommended that you supply this action in all of your dashlets.

Example 5
~~~~~~~~~

ADDING TITLE BAR ACTIONS

Introduce widget class and explain how to add it using the JS controller or FTL file

Custom client-side components
--------------

In the following two examples we have seen how we can re-use the standard capabilities provided by Share by instantiating an instance of specific widget classes in our dashlet.

This is a powerful capability, but if we want to add our own custom client-side behaviours then we need to step outside what is provided by Share and start implementing our own JavaScript classes.

Fortunately Share provides a base class which we can extend, named ``Alfresco.component.Base``. This abstract class does the work of binding an object instance to a particular Dom element and allows us to provide a custom function which will be fired when that element is available for scripting.

In addition, the base class provides functions for setting configuration attributes and retrieving any text strings required for the UI from localised message bundles.

Until now, we have re-used JavaScript objects already available on the dashboard page, and therefore Wehave not had to worry about where their definitions are stored and how they are included. We will need to consider these questions here, as well as how exactly the classes should be declared.

Defining your client side component
---------------------

Rather than including the definitions of our components inline within the HTML output by the dashlet, the standard methodology in Share is to define your classes externally in standalone JavaSckript 

