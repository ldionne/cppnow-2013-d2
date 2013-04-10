<!SLIDE subsection>
# The algorithm


<!SLIDE>
# Disclaimer:
I am not the author of the algorithm. All the references are given at the end.


<!SLIDE>
.notes Specify that we'll be adding annotation on the graph to encode more
information like segmentation and gatelocks. Precise that we'll start with
the most basic graph and add annotations as we go to improve the algorithm.

the basic idea is to build a graph where:

* vertices represent synchronization objects
* an edge from u to v means that a thread acquired v while holding u
* a cycle in the graph will represent a potential deadlock
