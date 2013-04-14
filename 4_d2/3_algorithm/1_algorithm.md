<!SLIDE subsection>
# The algorithm


<!SLIDE>
# Disclaimer:
I am __not__ the author of the algorithm. It is presented in:

"Detection of deadlock potentials in multithreaded programs",
IBM Journal of Research and Development, vol.54, no.5, pp.3:1,3:15,
Sept.-Oct. 2010


<!SLIDE smbullets incremental>
.notes Specify that we'll be adding annotation on the graph to encode more
information like segmentation and gatelocks. Precise that we'll start with
the most basic graph and add annotations as we go to improve the algorithm.

## The basic idea is to build a graph where:

* Vertices represent synchronization objects
* An edge from `u` to `v` means that a thread acquired `v` while holding `u`
* A cycle in the graph represents a potential deadlock
