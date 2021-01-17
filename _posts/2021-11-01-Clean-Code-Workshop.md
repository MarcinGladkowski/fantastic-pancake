---
layout: single
title: "Clean Code workshop"
published: false
---
At the 13th of december i participated in Clean Code and Architecture workshops done by Docplanner. Yep, very relevant topic
for each developer. The practicing of writing the clean code is the one of most grateful think for you, colics and your business.
The clean code allows you to make easy changes, maintainable things which you have time to forget. This is the skill to practicing 
every day.

Let's focus on workshop. The repository which was prepared by **Docplanner Tech** you can find [here](https://github.com/MarcinGladkowski/clean-code-architecture)

Let's focus on content!

# Object calisthenics

To get more knowledge about that I found a great blog post of **Willam Durand** -> [here](https://williamdurand.fr/2013/06/03/object-calisthenics/)
Each rule looks great, but using it can be more complicate.

In the shortest way. It's about follow to 9 rules:

1. Only One Level Of Indentation Per Method
2. Don’t Use The ELSE Keyword
3. Wrap All Primitives And Strings
4. First Class Collections
5. One Dot Per Line
6. Don’t Abbreviate
7. Keep All Entities Small
8. No Classes With More Than Two Instance Variables
9. No Getters/Setters/Properties

Below I described some examples of providing some of these rules on workshop and this test project.

### Only One Level Of Indentantion Per Method

It's a great advices except SOLID, KISS etc. I always try to avoid to many levels and **indentantion** and 
using **else** block.

Too long methods and functions are not a very simple and clear to read. We should to try to extract block of codes to 
simple methods. Looks simple but there is a lot of patterns of refactoring like **extract function** from [Refactoring Book](https://refactoring.com/)
(Martin Fowler).

*Pro tip: If you are using PhpStorm there is shortcut to [extract code as function](https://www.jetbrains.com/help/phpstorm/extract-method.html)*

Commit: [acb1aea](https://github.com/docplanner-workshop/clean-code-architecture/commit/acb1aea47fdbb802b2717b0c249ff6bcbeebedd5)
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/diff_3.png" alt="">
</figure> 

### Don't Use The ELSE Keyword

This simple change cam simplify a code to read and testing it. Less cyclomatic and cognitive complexity.
But we don't forget about *return* statement, because this technique allows us to safely execute code. 

Commit: [a566791](https://github.com/docplanner-workshop/clean-code-architecture/commit/a566791fb121bc6469447a5ae400e682fe5a6bab)
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/diff_2.png" alt="">
</figure> 

Another example of this technique. It's mean to fast quit from method when relevant data are missing.
```php
public function doSomething($params): ?someType
{
    if (empty($params)) {
        return null;
    }
}
```


### Don't Abbreviate

Using a meaningful variables names can be very helpful. What's we have done with code:
[0d1b74f](https://github.com/docplanner-workshop/clean-code-architecture/commit/0d1b74f3cb706a98f870421bba34061bcf5b1def?branch=0d1b74f3cb706a98f870421bba34061bcf5b1def&diff=unified)
[120a26e](https://github.com/docplanner-workshop/clean-code-architecture/commit/120a26ed7d1da0a31a983341419d2560b09ac52a?branch=120a26ed7d1da0a31a983341419d2560b09ac52a&diff=unified)

Meaningful names changes a readability a lot:

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/diff_1.png" alt="">
</figure> 

### Wrap All Primitives And Strings

When I heard this my first thought was - Value Object. The convention of [Value Object](https://martinfowler.com/bliki/ValueObject.html) comes from DDD (Domain Driven Design) techniques. Shortly, It's about to wrap meaningful values - usually they can be store in primitive values.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/diff_5.png" alt="">
</figure> 

### No Getters/Setters/Properties

  Since I trying to create immutable objects like **VO** there are any getters and setters. The state is usually 
passing by *__constructor*. The same thing is about to *wrapping all primitives*. We can follow this rule by using DTOs. 

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/diff_4.png" alt="">
</figure> 

Some rules can be hard to follow them for example this one about *only two instance variables*. Usually in bigger project exist specialized services 
doing to much things (the SRP - Single Responsibility Rule is break also) with a lot of some dependencies.


# Architecture

I am sure, all web developers knows at least one architecture pattern MVC (Model-View-Controller). When I write my first 
code as a Junior developer I pasted a lot of code to controllers. I think a lot of developers made mistake like this.
If you get some knowledge and experience, you will see the bad side of this solution. In my opinion each should meet this 
problem to saw what's wrong with this architecture. I don't say it's bad patters but have some limitations. Many of developers
cannot have idea *where should I put this code?* because they have only three official layers: *model, controller, view*.
What's the most dangerous to put all logic code into controller? The code isn't reusable. 

What we can do in this case ?