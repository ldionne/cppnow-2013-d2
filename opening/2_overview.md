<!SLIDE bullets>
# Overview

<!-- explain the type of deadlocks we're targeting -->
* What are resource deadlocks?

<!-- explain how they are non-deterministic bugs that only appear under
     certain conditions, with examples. -->
* Why are they so vicious?

<!-- review existing algorithms to detect them live and other ways to
     enforce correctness like enforcing consistent lock ordering. -->
* Typical ways of dealing with them

<!-- introduce d2, its purpose and its scope -->
* Introducing d2

<!-- give a bird's eye view of the large components. explain the high level
     flow of the events from the application to the repository, and then
     how the repository is analyzed with an utility post-mortem.
-->
* Overall design of the library

<!-- justify the decision of using _intrusive_ dynamic analysis.
     (integration to _custom_ lock classes; it's relatively easy with
     metaprogramming; we could always write a wrapper to automatically
     instrument code if we wanted to)

     note: is this really pertinent for the presentation?
-->
* Why intrusive?

<!-- present the actual algorithm. begin with the basic goodlock algorithm,
     and then extend it to several threads. then, present the final version
     filtering some false positives, as presented in the IBM paper. -->
* The algorithm

<!-- present the API of the library, i.e. the lock concepts, mixins, etc...
     must also present how to integrate with existing thread classes, even
     though there is no easy way to do this currently -->
* Integration with existing code

<!-- this is a _MUST_. seriously. I also need to show benchmarks with and
     without the logging enabled, to show that the impact on performance is
     not too large (which is not the case actually!).
-->
* Real world examples

<!-- - types of deadlocks we know we don't currently detect
     - types of deadlocks we won't even try to detect since it would escape
       the scope of the project
     - difficulty of having meaningful diagnostic information (stack traces)
     - potential scalability problems with the algorithm
     - distributing across several machines for large applications would
       add _tremendous_ value to the project, but it is much harder too
-->
* Future improvements and known limitations of d2

<!-- explore the possibility of creating a toolbox for performing intrusive
     dynamic analysis
     ultimate goal:
        generate orthogonal dynamic analysis tools using a DSEL defined in dyno
    possible use cases:
        - benchmarking/checking memory allocation
        - access to shared variables to detect race conditions (this might be
          impossible because it would require too much modification)
        - gather statistics during program execution
 -->
* Towards dyno

<!-- - hawick_circuits algorithm
     - present the current state of the DSEL in dyno
-->
* Bonus material (if it fits)
