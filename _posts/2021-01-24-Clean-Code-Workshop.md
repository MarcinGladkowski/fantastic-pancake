---
layout: single
title: "Clean Code workshop"
published: true
---
At the 13Th of December I participated in Clean Code and Architecture workshops done by Docplanner. Yep, very relevant topic
for each developer. The practicing of writing the clean code is the one of most grateful think for you, colic and your business.
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

### Only One Level Of Indentation Per Method

It's a great advises except SOLID, KISS etc. I always try to avoid to many levels and **indentation** and 
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
What's the most dangerous to put all logic code into controller? Then developers put business logic also to controller, 
model and view!. Developing in this way makes a lot off mess in our code and if we want to apply some little change
we have to look it very careful! The code isn't reusable.

What we can do in this case ?

I hope you heard about **SOLID** principles. These rules are independent from programming languages. Only interesting thing is
this is the most popular requirement question for programmer. If you don't know this rules, hurry up! You should memorize it.

Using SRP (Single Responsibility Principle) and DI (Dependency Inversion) we can easily decouple our logic and relation between 
classes. 

Some steps of refactoring to make our code clean:
* Moving methods to external class (service)
* Not using lower level dependencies in a service
* Depend in Abstractions, not on implementations (ex. create and interface)
* Moving out Request handling out of the Controller
* Entities refactoring (no getters/setters, guard invariants (valid domain state)...)

Look at this example controller, please. Looks good, but can we do better ?

```php
class Controller extends AbstractController
{
    public function index()
    {
        return new JsonResponse('ReallyDirty API v1.0');
    }

    /**
     * @Route("/doctor", methods={"POST"})
     * @param Request $request
     * @return JsonResponse
     */
    public function addDoctorAction(Request $request)
    {
        $doctor = $this->createDoctorFromRequest(
            $request->get('firstName'),
            $request->get('lastName'),
            $request->get('specialization')
        );

        $this->save($doctor);

        return new JsonResponse(['id' => $doctor->getId()]);
    }

    /**
     * @Route("/doctor", methods={"GET"})
     * @param Request $request
     * @return JsonResponse
     */
    public function getDoctorAction(Request $request)
    {
        $doctor = $this->getDoctor($request->get('id'));

        if (!$doctor) {
            return new JsonResponse([], 404);
        }

        return new JsonResponse(
            [
                'id' => $doctor->getId(),
                'firstName' => $doctor->getFirstName(),
                'lastName' => $doctor->getLastName(),
                'specialization' => $doctor->getSpecialization(),
            ]
        );
    }

    // and more and more...
```

What the cons of this solution:
* If have a lot of endpoints it can be verrryy long class (thousands lines of code...)
* The controller depends of Request class. 
* We have to decide which type response we want JSON, XML or something else.
* We have to parse incoming data, but maybe it not only from HTTP request ?

There is a time for **Action Domain Responder**

This architecture pattern is quite new for me and was I found it isn't very old (like MVC - 1979).
But great to get new knowledge about architecture. Invited and described by [Paul M. Jones](http://paul-m-jones.com/)
in 2014 using **PHP**! oh yeah!

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/adr.png" alt="">
  <figcaption>https://github.com/pmjones/adr</figcaption>
</figure> 
And the MVC pattern
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/mvc.png" alt="">
  <figcaption>https://medium.com/@eliseharris99/the-model-view-controller-mvc-design-pattern-3c32c5eccbac</figcaption>
</figure> 

I am not a super expert in Symfony framework but some Docplanner created a blog post about
implementation ADR [here](https://medium.com/swlh/implementing-action-domain-responder-pattern-with-symfony-606539eea3a7)

But the main core of ADR is above. The main action class has a dependency injection with are *Doctors* and *DoctorFactory*.
Focus on remember to using *interfaces*. The one thing is strictly tie with framework is an this *annotation*. 
This action looks very clear, the input and output are specified DTO classes.
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/add_doctor_action.png" alt="">
</figure> 
DTO of input.
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/add_doctor_input_DTO.png" alt="">
</figure> 
Dto of output. Very simple to use it to make an system response in each kind of: JSON, XML etc.
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/clean_code/add_doctor_output_DTO.png){: .align-center}



How look controller now ? You have right - it's not necessary.
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/clean_code/controller_after_changes.png" alt="">
</figure> 

_This post is a some shortcut to memorize these things. 
It can looks a little chaotic but it's first post for 2021._

Full presentation: [here](https://docs.google.com/presentation/d/1RkrPrKNUL1DVmHuRFA6RC5eHPRG0xUiNsZLEfI0CZzw/edit#slide=id.gaeea1ef32b_1_1)

The workshop was great! Thanks for all Docplanner team!

_Marcin_
