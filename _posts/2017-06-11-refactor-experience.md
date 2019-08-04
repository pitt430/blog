---
layout: post
title: Refactoring Practice
date: 2017-06-22
Author: peterli
tags: [quality]
comments: false
toc: true
pinned: true
---

Experience on refactoring

## 10 years core modules refactoring

Recently refactored a core function module of the company for nearly 10 years, and stepped into many holes. There were times when I felt like I couldn't do this refactoring, times when I was under a lot of pressure, thinking I was going to quit programming.
But fortunately, I still stick to it, when I think back to writing this project, I have overturned it three times, that kind of mental journey really only experienced to know, really suffering. In retrospect, the pit I stepped into was more of an experience problem than a technical one.

### Attitude

Looking back on this project, I think the attitude is the most important, the technical problem is the second. Why do you say that? For more than 10 years old functional modules, the most complex is actually the business logic, not the technical implementation. Therefore, for the refactoring of the old system, you first need to sort out the business logic accumulated in this module for more than ten years, which in itself gives the refactoring an invisible pressure. In addition, it is the core business module. Less business logic will lead to a decrease in online revenue. This series of background, make the psychological pressure of refactoring is really big.

In retrospect, the best way to refactor a project is to carefully tease out all the business logic and then mentally map it out so that you can see the business logic for more than a decade. Understanding the business logic can be a useful, if not decisive, part of your system redesign and coding process.

When it comes to mindset, no matter what project you're working on, even if it's refactoring, there's a scheduling issue involved. Sometimes it's hard to be a refactor when you're asked to give a timeline before you fully understand the business logic yourself. In this case, I suggest that we should communicate fully with our superiors. It would be best if we could extend the time for scheduling. If not, take the initiative to report your progress on a daily basis and let superiors know your progress. In fact, to give you a schedule, is not to control the progress.

When you give the schedule, there are likely to be a lot of things that didn't happen when we estimated the schedule at that time. At this time, we usually have two chofices: one is to rush through the problem, the other is the best way to calm down and think about it. In fact, when you find that the alternative is a better way to deal with, but this way can not be completed under the current schedule, then as the overall function of the development refactoring, you should have their own judgment. Even if the delay is due to the fact that you did it, you should stick to your choice as long as you do the right thing, which is conducive to system expansion and stability. I think if you delay because of the right insistence, I don't think the company will blame you as long as you give your reasons. The worst thing you can do is write something in a panic, and it's full of holes.

So sometimes I feel that developing or refactoring a system is really attitude, so that I can have my own persistence and not make a wrong choice under the scheduling pressure from superiors. Especially for refactoring projects, if you don't have a calm mind, the system is definitely not good.

### Skills

I think the experience of refactoring is far more important than the technical strength, because one experience can save you a lot of unnecessary trouble. Before I tell you what I've learned, let me ask you a question:

There is a problem when you refactor, and it is better to fix it, but not to fix it will not affect the results of the project. Excuse me, at this moment you are to solve this problem, still do not solve good?

Some people will analyze: it depends, if I have time I will solve it, if I don't have time I will skip it. The people who give this kind of answer are the giants of theory and the dwarfs of action, and they can be concluded that they have not experienced actual combat. Because the analysis is very much in line with the dialectical ideas of marxism, which is also true. But such a solution is not useful enough in the real world.

In this case, my advice is not to. To be precise: for anything that doesn't interfere with your goal of refactoring, don't do it. Because you never know how deep the hole is! When you don't know how deep the hole is and you have a platoon behind you, the smartest thing to do is not to do it. When you've done what needs to be done, come back to the problem and if you can solve it, try to solve it. But if you find that the hole is really deep, you can return decisively, and you have already done the work, there will be no scheduling pressure. Such practice leaves oneself enough back road, oneself can advance can retreat, can attack can defend. But if you choose to step in the pit in the first place, you may be setting yourself up for a quagmire.

So I suggest that we don't do it under unclear circumstances, not that we are lazy. It's about understanding what your purpose is and getting things done with limited resources (time).

### Technology

Technology comes last, because I really don't think technology is particularly important in refactoring. At least in the case of my refactoring, 60 percent of my work was mostly due to rework caused by my mindset or lack of skills. The techniques involved in refactoring my projects were done in less than 10% of the time. Looking back, I feel really sad.

The technique in refactoring is more about using design patterns to render complex business logic in concise code. Simply put, it's about using design patterns to carry complex business logic and keep the code as clean as possible.

What is a good system refactoring? In fact, there is a very good saying: open to expansion, closed to modification. This means that others can only extend your code, not modify it. Why are many old systems getting more complex? More logic? The reason is that he writes code that allows others to modify it. It might be very abstract, but let's do an example.

```csharp
If (type = = apple) {

/ / deal with apple

} else if (type == banana){

/ / deal with banana

} else if (type ==... ) {

/ /...

}
```

The code above simulates the process for peeling fruit. If it is an apple, then it is a method of peeling; If it's bananas, it's another way to peel them. If you need to deal with other fruits later, you'll add a lot of if and else statements, which will eventually make the whole method smelly and long. If different varieties of the fruit have different methods of peeling, then there will be many layers inside.

As you can see, the above code does not meet the "open to extension, closed to modification" principle. Every time you need to add a new fruit, you can modify it directly from the original code. Over time, the entire block of code becomes stinky and long.

If we were to make an abstraction of peeling a fruit, peeling an apple is a concrete implementation, and peeling a banana is a concrete implementation, the code would look like this:

```csharp
Public interface PeelOff {

Void peelOff ();

}



Public class ApplePeelOff {

Void peelOff () {

/ / deal with apple

}

}



Public class BananaPeelOff team PeelOff{

Void peelOff () {

/ / deal with banan

}

}



Public class PeelOffFactory {

Private Map Map = new HashMap();



Private init () {

Init all the Class that implements PeelOff interface

}

}



cars only



Public static void main () {

String type = "apple";

PeelOff PeelOff = PeelOffFactory. GetPeelOff (type); / / get ApplePeelOff Class Instance.

PeelOff. PealOff ();

}
```

This implementation makes it impossible for others to modify our code. Why?

Because when it came time to peel the watermelon, he found that he could only add a new class to implement the PeelOff interface, and could not modify the original code. In this way, the principle of "opening to expansion and closing to modification" has been realized.

In fact, the above design pattern is the most basic design pattern: abstract design pattern. For complex business situations, there are many other design patterns, such as the proxy pattern, the decoration pattern, and so on. But patterns come down to your understanding of the business and your ability to abstract, and if you understand the business enough, you will write and write about a design pattern even if you don't know it.

## Conclusion

The experience of refactoring at this point was painful, but it wasn't. I suddenly exclaim that refactoring is still very technical, and the technical requirements are not so high. Refactoring is more of a test of understanding the business and further application of abstract thinking. Design patterns can be learned little by little if the business is well understood and abstracted. If it's the other way around, there's no way to do it.

How hard it is to refactor, and move on.
