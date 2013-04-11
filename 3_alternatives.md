<!SLIDE subsection>
# Alternatives


<!SLIDE bullets incremental>
# Do nothing

* Not a great solution


<!SLIDE bullets incremental>
# Never hold more than one lock at once

* Only possible for trivial programs


<!SLIDE bullets incremental>
# Determine a hierarchy among locks and respect it

* Error prone
* Can be impossible for complex programs


<!SLIDE bullets incremental>
# Disturb thread scheduling to provoke hidden deadlocks

* That's actually a good idea, but it shouldn't be necessary with our approach


<!SLIDE bullets incremental>
# Use an algorithm to break deadlocks when they happen

* Runtime overhead required to check for deadlock conditions
* The policy for breaking deadlocks can't be pretty: kill the thread
* Misses the point: deadlocks are a bug, not a runtime mishap


<!SLIDE bullets incremental>
# Intel® Inspector XE

* Not as flexible as a library-based approach
* Huge overhead
* Detects deadlocks involving up to 4 threads only
* Proprietary
* Exact capabilities unknown (for deadlock detection)


<!SLIDE>
# Coverity® Quality Advisor
<!--
    talk about Coverity, if some info can be found for god's sake.
    it's like they wiped all useful info from their site to give
    us a marketing talk overdose.
-->
