<!SLIDE subsection>
# Bonus material


<!SLIDE>
# `dyno`: a toolbox for generating dynamic analysis frameworks
<!--
    the ultimate goal is to be able to define orthogonal dynamic analysis
    tools using a DSEL. uses cases are:
        - benchmarking/checking memory allocation
        - access to shared variables to detect race conditions (this might be
          impossible because it would require too much modification)
        - gather statistics during program execution
-->



<!SLIDE>
# The `hawick_circuits` algorithm


<!SLIDE>
<!-- justify the decision of using _intrusive_ dynamic analysis.
     (integration to _custom_ lock classes; it's relatively easy with
     metaprogramming; we could always write a wrapper to automatically
     instrument code if we wanted to)
-->
# Intrusiveness: a design decision
