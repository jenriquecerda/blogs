## What to expect from an Agile Team

Over the past few years, the rise of software development and the importance of it in organizations, have given way to Agile methodologies. Agile methodology in software development refers to an iterative discipline where projects evolve through team collaboration, including business owners and software engineers. It encourages frequent feedback and adaptation to current situations where downtime is minimized. Planning and estimation is done by engineers and they are responsible for deliveries within that timeframe.

### Getting the most out of it

Agile teams are composed of software engineers that are committed with each other and most importantly, at an individual level with themselves. They care about their product quality and what their product says about them not as developers but as a professional peer.

Deliveries are divided by sprints and sprints by **user stories**. A user story is presented to the customer as he would normally know it, without any technical details. So for example, a web application where users would need to get registered, could be presented as: "Build user registration module", and that could be decomposed into more specific stories: "User can sign up with email and password", "User can sign up with Facebook", "User can reset their password", or many others. Maybe the customer does not care right now that users can sign up with Facebook but he does cares about users signing up with their email address. The important point here is that a customer will precisely know whats being worked on and he will have given his input on what to work right now since financial resources are limited most of the time and the goal is to maximize delivery. This user stories are estimated and the customer gives his approval. This is an important point. Most probably a customer will not know about databases, APIs, etc. So instead of presenting him with a user story that says "Build an API that allows requests to register users", he is presented to a more down-to-earth story for him to know exactly what his money is doing.

Some blocking will occur for many reasons, sometimes it will be as simple as a lack of knowledge of the business context, some other times external dependencies change, or many other situations. What's important here, is that even though Agile or non-Agile teams have those blockages, an Agile developer will know how to adapt or even communicate that to his team and finally to the customer. When this happens, a choice is taken and communicated to the customer, either to continue on the blockage or carry on with another user story while the external factor gets resolved. This will maximize coding time and keep the customer satisfied with deliveries.

By now, a point has been made and shown, quick feedback is the key to Agile development. I've witnessed non-software companies start a project with a few engineers, as time goes on, this teams usually grow from a couple of engineers to a small army, taking a big bite of profits. This happens mostly because a bad misconception is handed down to the customer, "we need more manpower". This would make sense probably if the business has grown and more requirements are needed, but in many cases a big blob of code starts to take life of itself and more and more engineers are needed to handle the same modules for the same business. Having a disorganized team is not the answer, efficiency per developer diminishes and this translates to a bad allocation of money per resource. So be sure of this, hiring more developers is not the solution. But having a good Agile team is. If one more developer is needed, an informed and sustained reason is communicated to the customer. He will finally either give it a go or not.

### So how does a non-technical customer gets assured?

One of the key features of having rapid feedback is testing. You can find an excellent blog on this subject named [**"On TDD & Writing Good Tests"**](https://dynamic.tech/articles/tdd/writing-good-tests). Having a good testing suite allows engineers and business owners to know the product being delivered works. As user stories, tests must be written for the customer to know what is going on in the inner piece of code.

```ruby
describe "TimeMachine" do
  before(:each) do
    @time_machine = TimeMahine.new(:date => Time.now)
  end

  it "travels ahead of time" do
    @time_machine.travel_days(2)

    expect(@time_machine.current_time).to equal(Time.now.day + 2)
  end
end

```

Running this test and having a green output, assures the customer this functionality works. The test is a literal description of what `TimeMachine` does. I can assume the customer has given the team a project to work on a TimeMachine and what he finally cares about is, it can travel ahead in time. In this case the test gets read by the customer as "test TimeMachine can travel ahead in time". Easy to understand it works right? The heavy work will be done by the engineer and he will have to make it pass. Many more tests will get written for this module and the customer, being a non-technical person, can be assured that the module he paid for, works.

### Is your product growable?

Business needs change trough time. Being a hiring customer, this **MUST** be in their radar when hiring a team. Does this team follow best practices to write pluggable and scalable code? Or will I have to hire another team to fix and adapt new modules to the product? This is one of the key features a good developer must pay attention to when writing code and such coding practices are probably not know by a non-technical customer. My blog on [**Interfaces**](link) tries to explain this in a simple way, as well as some other pointers for non-technical users. Take a look at it and be sure your team follow this principles, known as **SOLID**. Having this type of code, clean and tidy, is an Agile team characteristic. This team will leave your code prepared for them or for future developers to grow more modules or functionality.

### Short and long-term advantages

I hope I have made clear the short-term advantages of having an Agile team has. But most importantly, I hope the reader takes some notion and sense of what he must look and ask for when hiring a team. Code will linger on for future developers and having a good structure will last while the business lives. This sets the base and the tone for future deliveries and hopefully the reader will know when are new developers needed and when not.
