# Software Development Process

## Software Teams
- Who are the members of a software development team? ASK!
- Developers! (yebbut... what can developers do?)
  - Front end (html/css) Develops what the user sees. Interface and design.
  - Back end (app & DB – Rails, for this course – but there are others) Provides a base for the front end developer to build upon.
  - both?
- Designers (can developers be designers?) Will design the appearance of the product.
  - UX - Designs User Experience, will work closely with front end and design teams
  - UI - Designs User Interface, will work closely with front end and design teams
- Sysops – server management
- Project manager - Manages resources and time.
- Product manager - Takes control of the product, decides on the features to build.
- Database Analyst -Works with the database.
- Development manager-Similar to the project manager, but works closely with the dev team.
- The QA tester - Checks code, often by using the software as a user would and creates bug reports for developers.
 - Development Operations (Dev Ops) – after code has been signed off on by QA and is relatively bug-free, these people deploy this software to production, pushing it to a server making it available via the Internet; usually system administrators
- The Client - Bad software development teams forget the client is part of the team

### Others
- Development Manager, Project Manager, Solutions Architect, Technical Architect, UI Designer, UX Designer, Functional Analyst, Lead Developer, Developer, DBA, Quality Assurance, Deployment, Trainer, Domain Experts...)
- Some teams have all of these... some have one or two...


## Project management methodologies

### Waterfall development method
In the old days, software requirements were specified in a big book that took awhile to create, and then these specs went to the developers. This is an inefficient way to develop software, because you cannot possibly know ahead of time everything that will be needed. This process is called [waterfall](http://en.wikipedia.org/wiki/Waterfall_model).

![Waterfall](https://lh6.googleusercontent.com/4vI2oaCcyGSiKOV5kAgQ7CLUoKTwZVtfgfnrBtsn08uazGgvo5DHy0vrTCwQ1_188i0w5gHfrFi6p_QoFo7jvOKSgAG6oTGWXdpPAlHg8FiLZkU4CSa8i4F1SA "Waterfall Diagram")

* Requirements -  Requirements for the project are set, these will outline the project but are not specific.
* Functional Specification- This is a more detailed version of the requirements which will go into depth.
* Detailed design document.-Tells the developers what they are using and how to use it.
* Coding -  Can eventually begin after everything else has been set in stone.
* Testing - Completed code is tested against the original requirements.

### Agile development method
The [agile development](http://en.wikipedia.org/wiki/Agile_software_development) process takes an iterative approach. A requirement is set, it is implemented, tested, and refactored.This cycle is repeated until the product is complete. This is a much more flexible approach. Requirements are constantly being created and developers respond quickly to them.

![agile methodology](https://lh4.googleusercontent.com/n7nGFgC0WjFehArQ-YpF6amxpX7ssv3VI8G7Yd-zDlxs3yJK2hAy-fDiRZ-6JKqdNzidcbKJPEIsSzmWviVWI7WRJ9ZA6fvxjlCxgldYxm9CBxO9trT61QFlvA)

#### Agile Manifesto

"We are uncovering better ways of developing software by doing it and helping others do it. Through this work we have come to value:
    `Individuals and interactions` over `Processes and tools`
    `Working software` over `Comprehensive documentation`
    `Customer collaboration` over `Contract negotiation`
    `Responding to change` over `Following a plan`
That is, while there is value in the items on the right, we value the items on the left more.""

(exercise... make a paper aeroplane using Waterfall and Agile processes)

### The Scrum
[Scrum](http://en.wikipedia.org/wiki/Scrum_(development)) is a development methodology and subset of agile. 

The daily scrum/stand-up will allow each member of the team to take 2 minutes to reflect on their progress, their blockers and where they intend to go next.

![The Scrum Structure](https://lh5.googleusercontent.com/bun08_PrsvZaZosJRqKvZKgkhNDeYD3bVsnGHV2JzmGCF72S1Da3OwUQEno2bEQy89t-MRUpfIEyBXwTZ5oBOBXeHc_39h1hXWrpeArfJfgFCfHCEmUy4kWO-A "THE SCRUM")

![The Scrum Process](https://lh3.googleusercontent.com/XhlGKtWQC6kXZ84mN3aSzVqnZVPXSqJDJT0rAiWu3bxmajD9AaV-qfJeevYPiT9NLPs1DufWCUJ4ZuLUFJhZhghOazA_wIXXcWFBI-pe3dL6fIOmmj2Pzmmo_Q "THE SCRUM")

#### Scrum Rules

* meet at same time and place each day
* the meeting starts/finishes on time (latecomers beware!)
* meeting length is fixed to 15 minutes
* each team member answers three questions:
  1. What have you done since yesterday?
  2. What are you planning to do today?
  3. Do you have any "blockers" (i.e. stumbling blocks)?
* blockers are documented by the Scrum Master
* then resolved outside of the scrum meeting

### Testing
TDD (Test Driven Development)- This means writing a test that fails because the specified functionality doesn’t exist, then writing the simplest code that can make the test pass, then refactoring.You repeat this Red-Green-Refactor loop over and over until you have a complete feature.

BDD (Behaviour Driven Development) - This means creating an executable specification that fails because the feature doesn’t exist, then writing the simplest code that can make the spec pass. You repeat this until a release candidate is ready to ship.




