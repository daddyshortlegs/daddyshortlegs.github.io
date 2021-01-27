---
layout: post
author: andy
---
# The perils of UI testing

UI automation testing has a fairly bad rep in our industry, but is it fair? Or is it because we are doing it wrong?

I’ve been involved in UI testing in some form or another since about 2008. Back then we were working on a large online (greenfield) Journal system using agile methods. For reasons I don’t want to bore you with it was decided to use Selenium to test via the UI. We used Test Driven Development for the Java code and then wrote an ‘acceptance test’ that proved the feature through the UI. The acceptance test was written using the Selenium IDE plugin in Firefox. It was kind of a record playback system. The build system was fairly clever and was able to run these ‘scripts’ in an automated fashion. We all thought it was cool.

Fast forward a few years, different company, different tech and we’re now wanting to do automated testing via the UI for a Bingo app on iOS. We picked Calabash and Cucumber and we wrote loads of UI tests. We ran it on our build server and demoed it to the rest of the company in our showcases. We all thought it was cool.

A bit later, same company, different client, different technology, we wanted to do a similar thing for a Javascript webapp Sportsbook. The testing technology isn’t massively relevant, but someone read a blog post on it and got excited by it because everyone else in the Javascript community was using it. There was a demo, a lot of people thought it was cool. However, a few were getting a little weary.

Fast forward to the present, again UI testing was requested for a Lottery app. A different technology was picked, different people were involved. It got demoed. People who were fairly new to the company thought it was cool. Those who were paying the bills were pretty peeved.

Just what is going wrong?

## Testing via the UI is conceptually easy, but in practise quite hard

It makes sense doesn’t it? We write apps with heavy amounts of UI, so performing a manual regression test is time consuming and error prone. A fellow Smith once had the following sage advice:

*'Never send a human to do a machine's job'*

So the desire is to automate it to save time and effort. There’s no shortage of tools around that claim to make it easy and with next to no technical skills. These are companies that want to sell you something so of course would say that. Whilst initially easy, maintainability is likely to be an issue.

## UI tests are notoriously brittle

In our experience, when a test suite gets to a certain size, you’ll start to notice intermittent failures. This can be any number of things, but the main one is timeouts. Due to the asynchronous nature of the UI, it may not return in a time you expect. In which case your test will fail. You can increase the timeouts to get around this, but this brings with it other problems.

## UI tests are slow

As UIs are designed for human interaction and give user feedback, they are slow. Enter text such as 'flibble' into a username field and you can see it type. When you add in transitions and things like swiping and scrolling it slows even further.

## Doing too much

There’s a tendency to automate a lot of different cases through the UI, because this is how testers would do it manually. For example, trying different combinations for a password strength-o-meter. As previously mentioned, interacting via the UI is slow and sometimes brittle. 

If you’re using BDD tables (scenario outlines) this can make the problem much worse as it’s very easy to add another test example into the table which obviously will multiply the number of interactions in the one feature by the number of rows in the table. Potentially very expensive.

## Not enough technical discipline

There are manual testers who want to get into automated testing as this is where the demand is. Not only does it require upskilling on the technology used, but also requires some of the discipline we expect from developers. Without it we’ve seen duplication, highly coupled tests to the UI, and poor maintainability.

## Focusing on tools rather than collaboration

Don’t get me wrong, picking the right tool is essential, but even if you have the perfect tool, without collaboration, it’s going to be difficult. What we’ve found is that:

1. The automated tests will be written after development and QA have completed. The test suite will be playing catch-up.
1. No-one will trust the tests as they have had no input into them, or know what is in them.
1. Because developers have no oversight of them, this will cause them to frequently break as they’re changing the code, but not the UI tests.
1. The test will have no bearing on whether the Story is Job Done. Stories should only be considered ‘Job Done’ when all the tests pass and this should include the UI test for this particular feature, if it was agreed that it was needed.

The previous points are not just related to UI tests. Any tests that are done after the fact by a separate team will be problematic.

## Possible solutions
### Don’t silo the automation team from the developers or QA. 

Automation needs to be a joined up approach and QA automation and developers need to work closely together, ideally pair.
It should be the responsibility of the whole team, rather than just the automation engineer if a test breaks. If the UI changes in some way for a good reason, then it should be a team effort to change the test. If the test breaks, again, it should be a team effort to fix, not just an automation engineer.
This collaborative approach is what Agile was meant to be about.

### Avoid the UI as much as possible

As crazy as this sounds as we’re talking about UI testing we should try and avoid testing via the UI if possible. The password strength-o-meter mentioned above can be tested below the UI. It would be better to test the different combinations of passwords and their scores without the UI. The portion that we should test via the UI should be along the lines of: 

`Given a score is returned, then it’s displayed on the screen`

We could have many different test cases below the UI that would be quick and robust then 1 or 2 basic tests via the UI.

### Make the apps accessible

Apps should be accessible and provide IDs to select important elements on the screen. Anything that we need to click as part of our test, or to verify should have an accessibility ID. 

Tools such as Appium provide elements from the app as a DOM tree similar to a web browser. Like Selenium, Appium can select items by XPath. As with Selenium, you can make very brittle tests by making your XPaths too coupled to the structure of the page. You can use the ‘double-slash’ syntax in XPath to search for that element, but this is equally problematic as DOM trees can be huge, especially on Android. This can cause timeouts looking for elements. This is why it is highly recommended to use IDs instead.

### Don’t do too much

Don’t run too many combinations in a test case as mentioned previously. Pick important targeted examples that illustrate how it works.  If you must test a lot of different combinations of things, see if you can do it below the UI instead.

Speccing them out should be a collaborative process between BAs, developers and QA. 

### Don’t treat them differently to other tests

In particular make sure they can be run by anyone and make sure they’re visible to the team, preferably by running on a build server. It is no longer acceptable to say ‘but it works on my machine!’

### Treat the automation code the same as the production code

Automated tests should have had the same discipline applied that TDD emphasizes with developers. Things such as:

1. Using source control (should be a given in this day and age).
1. Committing to source control frequently. Preferably after each passing test.
1. Running on a Continuous Integration server (again should be a given in this day and age). 
1. Dropping tools and fixing breaking builds.
1. Following ‘Clean Code’ principles such as removing duplication and refactoring
1. Architecture. How to ‘layer’ the testing system so it is easy to reuse different components and easy to maintain.

### Create specifications, not scripts

The tests should tell you what the feature is in business terms, not how to get there. For example a login test might read:

```
Given I’m on the home screen
And I click on the ‘sign in’ element
And I enter ‘dangerousdave’ into the ‘username’ field
And I enter ‘flibble’ into the ‘password’ field
When I click on the ‘login’ button
Then I am on the home screen
And the ‘balance’ element contains ‘50 dollars’
```

This is a script, not a specification. It’s also highly coupled to the UI, which means if the UI changes the test breaks and the specification no longer reflects what it is meant to do. It’s better to use a more declarative approach, such as:

```
Given a username of ‘dangerousdave’
And a password of ‘flibble’
When I attempt to login
Then I should be logged in with a balance of ‘50 dollars’
```

If the UI changes in this instance, then this specification does not need to change.

BDD should talk in terms of business requirements, not clicking buttons. If using BDD, keep your features business English. 

### Avoid the network

Most apps require data from APIs. Problem is, most of the time you cannot rely on the data. APIs may be down or may not return what you are expecting. This will be a cause of instability. So mock your data!

### Collaborate. Don’t silo

Out of all the points, the non-technical one in the list is probably the most important. Collaborate on the specifications in your refinement sessions. Collaborate when writing the test. Collaborate if/when it fails. Collaborate, collaborate, collaborate. Don’t work in a silo!

## Conclusion

I started off this blog post by asking whether the bad reputation of UI testing is justified or are we doing it wrong.

I think the bad reputation is justified but a lot of the problems with it are some of the reasons I’ve mentioned above (so yes, we are doing it wrong). If your test suite is slow, no-one will want to wait for it to complete as we want fast feedback. Also, if they are intermittently failing, more and more time will be expended on trying to fix them, diverting precious resources away from high profile paying work.

If we focus the tests and limit their scope there is probably merit in doing them, but as with most things in life, do it in moderation.


