<!SLIDE subsection>
# The algorithm


<!SLIDE>
.notes Specify that we'll be adding annotation on the graph to encode more
information like segmentation and gatelocks. Precise that we'll start with
the most basic graph and add annotations as we go to improve the algorithm.

the basic idea is to build a graph where:

* vertices represent synchronization objects
* an edge from u to v means that a thread acquired v while holding u
* a cycle in the graph will represent a potential deadlock


<!SLIDE>
# deadlocks between 2 threads

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
                B.lock();
                B.unlock();
            A.unlock();
        });

        thread t2([&] {
            B.lock();
                A.lock();
                A.unlock();
            B.unlock();
        });


<!SLIDE>
# deadlocks between N threads

    @@@ cpp
        mutex x1, x2, ..., xM, xN;
        thread t1([&] {
            x1.lock();
                x2.lock();
                x2.unlock();
            x1.unlock();
        });

        ...

        thread tM([&] {
            xM.lock();
                xN.lock();
                xN.unlock();
            xM.unlock();
        });

        thread tN([&] {
            xN.lock();
                x1.lock();
                x1.unlock();
            xN.unlock();
        });
