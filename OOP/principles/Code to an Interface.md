# Code to an Interface

"Code to an interface, not to an implementation" - what does this mean?

A very simple example of this principle in action would be a database abstraction layer. We have interfaces to, say, *connect to database*, or *execute SQL query*. The exact implementations depend on local conditions e.g. what type of database we are using. So in this case the principle of 'code to an interface...' just means doing all your database-specific stuff in one (composition) separate (decoupled) place, and having your client code deal only with interfaces.

The same principle is at work in Zandstra's composition example of fixed/timed lectures/seminars. The solution in that case is to use Strategy pattern so that any given Lecture or Seminar (or other descendant of Lesson) takes as a parameter a CostStrategy implementing object. In the example there's a choice between FixedCostStrategy or TimedCostStrategy. NB the parameter type is CostStrategy, not any of the specific implementations. There will be a cost strategy, but we are not specifying *which* strategy. So we are free to instantiate a Lecture or Seminar with either cost type - or any future cost type that may emerge in future.

