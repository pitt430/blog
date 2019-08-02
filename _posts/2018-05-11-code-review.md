I will explain the benefit of code review based on my experience and the best practices.

# Why code review

Many teams or individuals don't do Code Review, and they still don't think it is meaningful or beneficial. There are several ways to look at this.

## The first is the team knowledge sharing perspective.

In a development team, composed of senior and junior devs, and each person focuses on different areas. How to let the senior of help junior? How do you keep everyone informed about things outside of your area of focus? How can someone leave and someone else take over quickly? These are the concerns of team managers.

Code review, on the other hand, is a great way to share knowledge. Through code review, the expert can directly point out problems in the novice code, and the novice can learn good practices from the expert's feedback and grow faster. Through code review, the front-end can also learn the back-end code, and those who do function module A can learn function module B.

Some experts may feel that giving novice code reviews is a waste of time and not a gain. In fact, the newbie's growth, can help seniors to do more task; Spend more time on code reviews may save your time on fixing the bugs. Good communication skills, the ability to find problems, and help others grow are all essential skills for technology transfer management or technical advancement, and code review can effectively practice these skills.

## Then it is the code quality perspective

Real projects are always understaffed and on a tight schedule, so automated testing and code reviews are often compressed, resulting in code quality issues, technical debt, and eventually double payments.

There are also people who expect manual testing after development, but for code quality, many problems cannot be tested by testing, only by code review. For example, the readability and maintainability of the code, such as the structure of the code, such as certain conditions triggered by the loop, logic algorithm errors, and some security vulnerabilities are easier to find and prevent through code review.

There are also those who feel that they don't need code review because they are good at it. For the best of us, having someone review our code allows others to learn good practices. When you explain your code to someone else while having them review it, you're also doing a review of your code. It's like doing math problems in school. The ones who get high marks are the ones who check them carefully after they're done.

## There is also the perspective of team specification

Each team has its own code specification, its own development specification based on architectural design, but over time, you find a lot of code that doesn't follow the code specification, a lot of code that bypasses the architectural design. Such as difficult to understand and non-standard naming, such as the three-tier architecture UI layer bypasses the business logic layer to directly call the data access layer code.

If the code that violates the specification is corrected late, it will be costly to modify it later, and the team's specification will slowly fade away.

Through code review, these issues can be identified and corrected in a timely manner to ensure the implementation of team specifications.

There are many other benefits to code review, but they are not exhaustive. I still hope to realize that Code Review, like writing automated tests, If you invest a little time in it, you will gain Code quality in the future and save the overall development time.

# How to do code review

Now many people have realized the importance of Code Review, but they just don't know how to practice it and what is a good Code Review practice.

## Make Code Review a mandatory rather than an optional part of the development process

I've tried to incorporate Code Review as part of the Code process for a long time, but it's only an option. You can merge Code into the master without Code Review. The result is that Code Review will be done only after I remember it. When I go to check, there are too many Code changes, which is very difficult to Review. In addition, even if there are problems in the Review, it is difficult to modify.

We now review the code as a necessary option of the development process. Every time we develop new functions or fix bugs, we open a new branch. There are two necessary conditions for the branch to be merged into the master:

- All automated tests pass
- There must be at least one Code Review pass, if it is a novice PR, there must be a senior programmer Code Review pass

By making Code Review a mandatory part of the development process, you can ensure that Code has had Code Review before merging. And the benefits of this pre-merge code review process are clear:

- Since a code review is required before each merge, the amount of code reviewed at one time is not too large, and the pressure on reviewers is not too great
- If problems are found in Code Review, the examiners hope that the codes can be merged as soon as possible, and they will actively modify the reviewed problems, so as not to contradict the Review results too much

If you find Code Review difficult to implement, try making it a mandatory part of your development process.
