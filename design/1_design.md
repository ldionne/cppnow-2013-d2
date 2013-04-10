<!SLIDE subsection>
# Design


<!SLIDE>
## concepts from Boost.Thread

    @@@ cpp
        boost::BasicLockable
        boost::Lockable
        boost::TimedLockable


<!SLIDE>
## high level concept based API

    @@@ cpp
        d2::basic_lockable
        d2::lockable
        d2::timed_lockable
        d2::trackable_thread

.notes explain how each wrapper has a mixin counterpart to accomodate every
use case, and that trackable_thread is for standard conforming thread
implementations



<!SLIDE>
## with recursive flavors

    @@@ cpp
        d2::recursive_basic_lockable
        d2::recursive_lockable
        d2::recursive_timed_lockable

.notes explain how each wrapper has a recursive counterpart that is a simple shortcut for the longer version


<!SLIDE>
# low level C API (bindings someone?)

    @@@ c
        d2_notify_acquire(thread, lock)
        d2_notify_release(thread, lock)
        d2_notify_start(parent, child)
        d2_notify_join(parent, child)


<!SLIDE>
# event generation and dispatch to the filesystem

    @@@ cpp
        dispatch(core::acquire(thread, lock))
        dispatch(core::release(thread, lock))
        dispatch(core::start(parent, child))
        dispatch(core::join(parent, child))
        dispatch(core::segment_hop(thread, new_segment))


<!SLIDE>
# organization of events inside a directory

    <!-- here, have a picture or an example of a repository to show
         the directory structure (per-thread and global events)
    -->


<!SLIDE commandline>
# mumbo jumbo on disk

    $ cat my_program/1    # thread 1
    22 serialization::archive 10 0 0 4 0 0 0 0 1 0 0 2 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 1 1 0 0 1 0 0 1 1 0 0 1 1 0 0 0


<!SLIDE commandline>
# `d2tool` speaks that mumbo jumbo

    $ d2tool --analyze myprogram
    <!-- output -->

.notes explain how d2tool loads the events, constructs the graphs and performs the analysis.
