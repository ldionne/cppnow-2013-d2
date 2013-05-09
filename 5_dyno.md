<!SLIDE subsection small>
# `dyno`: a DSEL for dynamic analysis


<!--
    the ultimate goal is to be able to define orthogonal dynamic analysis
    tools using a DSEL. uses cases are:
        - benchmarking/checking memory allocation
        - access to shared variables to detect race conditions (this might be
          impossible because it would require too much modification)
        - gather statistics during program execution
-->
