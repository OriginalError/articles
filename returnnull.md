# Never Return Null

## Never Return Null is a bold instruction. 

`Null` is the default value for an object variable in Java, therefore any plain old Java object (POJO) instance that is not completely initialized will be filled with null values in its object member fields.
Unless we are careful, there can be nulls everywhere, and null in this case indicates a mistake or, perhaps, laziness.

We must clarify that occasionally there is no alternative to null, given that POJOs often represent database entities and databases do allow and contain null values.
In these unfortunate instances, null must be returned.
Additionally, our test-support methods may need to return a null value in order to exercise the code.
Outside of these very specific scenarios – reflecting a database null value and automated testing – there is no good reason to return null from a method.

## Once we allow for the possibility that methods might return null, we immediately bloat our code.

When we check via `ob != null` or `Objects.nonNull(ob)`, we have extra – and unnecessary – code scattered through our codebase.
This, in turn, drives up cyclomatic complexity.
Cyclomatic complexity is the quantitative measure of the number of linearly independent paths within a section of code, typically measured on a method or class.
In other words, it is the number of branching decisions the code makes.
Every time we check for a null value before we start on a piece of code, there’s an additional branch that needs testing in our automated tests.
The worst part about this is that it is entirely self-inflicted.
If we *might* return `null`, then we *must* check for `null`; if we avoid it, we don’t have to check.
We’ll address in a bit how to deal with someone else’s code that might return null values.

## Another significant problem with returning a `null` is that doing so violates the contract of the method signature.

If we have a method signature of `public Widget getWidget()`, we expect it to return a `Widget`.
This is oversimplified because `getWidget()` resembles a POJO method, and POJOs often represent nullable database entities.
Consider instead a method signature such as:

`public Schedule calculateSchedule(LocalDateTime start, long widgetCount, BuildConfiguration widgetConfig, int minimumShipQuantity)`

Granted, we do not know the inner workings of this method, but since we create self-documenting code that indicates what it does, our imagination gives us a pretty good idea.
There is no reason to expect this method to return `null`.
Nothing in the signature hints that `null` could be returned.
Sure, there might be JavaDoc comments, and a `null` might be mentioned therein, but that touches on an entirely different subject: *Code Comments Are Lies*.
Additionally, violating the method-signature contract when the specified return type is an array or a collection is even worse.  In Effective Java (2nd ed.) Joshua Bloch states, “there is no reason ever to return null from an array- or collection-valued method instead of returning an empty array or collection.”

So how do we deal with a situation in which we might be tempted to return `null`?  Here are a few suggestions:

* Return an empty array or collection. This only applies in certain situations, but it is 100% the right call to make when applicable.
* Throw a runtime (unchecked) exception. If something has gone sideways enough that null is the only applicable return value, then an exception is probably the right choice.
* Use the Null Object pattern. This is not a popular pattern, but it is better than returning a null.
* Return an Optional. This is only slightly better than a null because it still adds bloat and cyclomatic complexity due to the optional.isPresent() check required to use it safely.
* Refactor the method to eliminate the possibility of a null situation.

Now that our code no longer returns null values, how do we deal with the fact that somebody else’s code might?
The short answer is to introduce seams, which simply means wrapping calls made outside of our code.
This is an important concept in both *Automated Testing* and *Defensive Coding*.
Within the seam, do requisite null checking and ensure that a non-null value is returned (or an exception is thrown).

We do not want to deal with null values mixed with our code.
A coworker from long ago, Lincoln Baxter III, once remarked, “Nulls are poison in complex applications.”
Writing code is already difficult; don’t make it worse.
No matter the temptation, avoid returning null.


