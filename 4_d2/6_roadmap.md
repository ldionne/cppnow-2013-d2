<!SLIDE>
# Limitations

<!--
    - types of deadlocks we know we don't currently detect
    - types of deadlocks we won't even try to detect since it would escape
      the scope of the project
    - difficulty of having meaningful diagnostic information (stack traces)
      efficiently, and difficulty to have them at all without debug symbols
    - potential scalability problems with the algorithm
-->


<!SLIDE bullets>
# Roadmap

<!-- also talk about generalizing to more than locks, but any resource -->
* Support more varieties of locks

* Detect more types of deadlocks

<!-- talk about efficient call stacks -->
* Provide meaningful diagnostics

<!-- mention at least TBB -->
* Provide integration with more libraries

<!--
    the ultimate goal is to be able to define orthogonal dynamic analysis
    tools using a DSEL. uses cases are:
        - benchmarking/checking memory allocation
        - access to shared variables to detect race conditions (this might be
          impossible because it would require too much modification)
        - gather statistics during program execution
-->
* Create a generic toolbox to ease intrusive dynamic analysis (dyno)
