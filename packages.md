# You Are Doing Java Packages Wrong

There are two main ways that engineers package classes within a Java project: by type and by feature.  
In my professional career an arbitrary sampling shows a heavy tendency to package by type.
Let's discuss the differences and why you are packaging your Java classes wrong.

## Package By Type, a.k.a. "What Is It?"

### Packaging by type means grouping all classes by the kind of class that each is. 

All controllers are packaged together.
All services are together, often separated from the service implementations, which are also packaged together.
All helpers, all exceptions, all validators, etc., are all packaged together with others of that same type.  
Using this blueprint, a typical project would look something like:  

* controller 
  * BarController.java
  * FooController.java
  * WidgetController.java
* dto
  * BarDto.java
  * FooDto.java
  * WidgetDto.java
* exception
  * FooCustomException.java
  * WidgetCustomException.java
* helper
  * BarHelper.java
  * WidgetHelper.java
* impl
  * BarServiceImpl.java
  * FooServiceImpl.java
  * WidgetServiceImpl.java
* model
  * Bar.java
  * Foo.java
  * Widget.java
* repository
  * BarRepository.java
  * FooRepository.java
  * WidgetRepository.java
* service
  * BarService.java
  * FooService.java
  * WidgetService.java
 
There are several reasons to avoid packaging by type.

* In order to add or change a feature, an engineer must open all of the packages, which often means scrolling up or down to see the packages and contents thereof.
* Each package contains classes that aren't closely related to each other.  These packages have low cohesion and tight coupling.  Low cohesion in a package means that it is broad and unfocused in what its classes should do.  Tight coupling between packages means that the classes in packages are highly dependent on classes in other packages.
* Each package has a much greater chance to be interdependent on several other packages, increasing package dependency cycles.  A package dependency cycle is a situation where two or more packages rely each other to function properly.
* When one opens the project and looks at the top-level package structure, there is no way to see what the application does.
* Packages may contain a single class, e.g. only one feature needs a validator or a custom exception.
* As an application grows in size, the number of packages remains roughly the same, while the number of classes within each package will grow without bound.
* Protected and package-private access scopes are effectively impossible.
* Grouping types together leads to the tendency to create abstract or genericized code when such additional complexity is not needed.  The ideal of DRY is achieved but KISS is violated.
* Implementation details become more important than high-level abstractions.  An engineer will automatically think to create classes of expected types without first considering the most appropriate solution to the problem at hand.

Another term for this packaging method is "package by layer".
Usually this is simply a different name for exactly the same thing – package by type – but occasionally one might see it described as grouping classes by literal architectural layers, e.g. UI, network, database, model, etc.
This variation still remains subject to the same reasons to avoid as packaging by type, with the added complexity that it is infrequently encountered and would therefore be more confusing.

### A Real World Metaphor

Imagine that a company organized itself along package-by-type rules.
How would such a company look?

* All of the chairs are in one room.
* All of the desks are in another room.
* All of the computers are in still another room.
* All of the documents are in yet another room.
* All of the humans are in the conference room.

Now it's time to place an order for some additional inventory.
1. One must enter the conference room and interview people to find someone in purchasing.
2. Take that person by hand, walk into the documents room, and search through the piles of paper to find the relevant contracts and documents needed.
3. Next, go to the computers room to get the appropriate computer, swing by the chairs room to pick up a chair.
4. Finally go find a desk in the desks room to get the job done.

Want to give someone a raise?  Expect a similar procedure.

Seems silly, right?
Nobody would run (or work in) a company like that.
That is what happens when code is packaged by type.
Sure, Java handles it all just fine, but it's a cognitive load on the developers who must keep track of everything spread all over the place.

## Package By Feature, a.k.a "How Is It Used?"

### Packaging by feature means grouping all classes for a feature together within the same package.

Obviously, there will be situations in which adding every single class for a feature to a package would make the package very large.
For example, if one particular feature required several validator classes, those would be good candidates to be added to a sub-package for sanity's and clarity's sake.
The important part is that they are all validators for the one feature.
Such a blueprint would create a package structure similar to:

* bar
  * Bar.java
  * BarController.java
  * BarDto.java
  * BarHelper.java
  * BarRepository.java
  * BarService.java
  * BarServiceImpl.java
* foo
  * Foo.java
  * FooController.java
  * FooCustomException.java
  * FooDto.java
  * FooRepository.java
  * FooService.java
  * FooServiceImpl.java
* widget
  * Widget.java
  * WidgetController.java
  * WidgetCustomException.java
  * WidgetDto.java
  * WidgetHelper.java
  * WidgetRepository.java
  * WidgetService.java
  * WidgetServiceImpl.java

Unlike packaging by type, there are several good reasons in favor of packaging by feature.

* Each feature is in its own package (and sub-packages).  An engineer needs only to open one package to see all of the functionality of the feature.  Adding a new feature does not impact any other feature of the application.
* As an offshoot of the previous reason, code navigation is much simpler.  All of the items needed for a given task are usually right there in the same package.
* The packages have high cohesion and low coupling – classes are related to others in the same package and unrelated parts of the application are separated.
* There are effectively no cyclic dependencies.
* If necessary, a feature could be removed by a single right-click on a single package.
* A reasonable understanding of the functionality included in the application is obvious by opening the project and examining the top-level packages.
* The number of classes in a package is limited to the needs of a specific feature.  If that number grows too unwieldy, the package may be refactored in a natural way into multiple packages or sub-packages.
* Packaging by feature allows the opportunity to utilize access scopes of protected and package-private.
* The code tends to be more simple.  Each feature consists of the requisite code without (as much) temptation to extract common functionality into base classes.  KISS reigns supreme, though there exists a potential risk to DRY.
* Staying at a high level of abstraction is a guiding principle in programming.  Doing so makes it easier to think about a problem, and thus emphasizes fundamental services over implementation details.  A direct benefit of this is that the application – as a whole and within its features – becomes more self-documenting.

## Summing It All Up

Hopefully by now the advantages of packaging by feature are both clear and persuasive.
Let's consider some edge cases.

One recurring type of class that often crops up in projects is the utility class.
The first thing to decide is whether the utility is useful to more or even all features, or is it relevant only to one.
If only one feature will make use of it, package the class right there with the rest of the feature.
However, if the utility is broadly useful, the one shared package that I'm willing to concede is util.
Outside that, there is little need for packages for a type of class.

One general rule to follow is to allow looking left.
This means that an item can depend on an item that lives in a package farther up the package tree, i.e. to the left within the package name.
The previous example of multiple validators being grouped into a sub-package called validator fits here.
Each of these validators should implement a common interface.
The interface, however, should live in the same package with the controller or service implementation that will be doing the act of validation.
The controller (or service) will autowire a list described by the common interface, which is resident in that same package.
Each validator must look one package to the left to import (and subsequently implement) the interface.

Finally, there are times when an entity from one feature is relevant to another feature.
A recent example of this comes from a new-hire boot camp I was in charge of.
Within the demonstration application, there is an entity of Movie which is a member of one or more Genre entities.
Likewise, a Genre may describe one or more Movie entities.
One solution is to allow the increased package dependency cycle that results from each class importing the other.
However, one might consider whether the genre in a movie really needs to have a list of all of the other movies related to that genre, or if, instead, a trimmed-down version of Genre might be more appropriate.
Likewise, within the genres feature, perhaps the movies associated with each genre may not need to have links back to each of the other genres to which it belongs.
Again, a Movie entity without the back-linked genres might be more appropriate.
DRY suggests to use the same entity class in each place, but the resulting complexity to prevent the ensuing glut of JSON data suggests that perhaps KISS is more important.
Consider whether an almost-duplicate version of each class contained within the relevant feature packages might be better.

In summary, if you package Java classes by feature, congratulations!
You are making your applications easier to understand and maintain.
On the other hand, if you package by type, there is room for improvement.
