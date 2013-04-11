<!SLIDE>
# A system for resource deadlock prevention
## ~~~CONFIG:author~~~, ~~~CONFIG:venue~~~


<!SLIDE>
# Overview

* Our target: lock order inconsistencies
* Alternatives
* `d2`: a library-based approach
    * Design
    * Algorithm
    * Usage
    * Real world examples
    * Roadmap


<!SLIDE>
## A note for the rest of the presentation:

For brevity, unlocking mutexes and joining threads will often be omitted.
When omitted, assume the mutexes are unlocked in reverse order of locking
and threads are joined in reverse order of starting.


<!SLIDE>
# So these are the same:

    @@@ cpp
        mutex A, B;
        A.lock();
            B.lock();
            B.unlock();
        A.unlock();

# and

    @@@ cpp
        mutex A, B;
        A.lock();
            B.lock();


<!SLIDE>
# And so are these:

    @@@ cpp
        thread t1([] {});
            thread t2([] {});
            t2.join();
        t1.join();

# and

    @@@ cpp
        thread t1([] {});
            thread t2([] {});
