<!SLIDE>
this time, we will augment the edge labels to record the set of locks held by
the thread causing an edge to be added to the lock graph.

a cycle is not valid if the gatelock sets of any two edges in the cycles
intersect, i.e. if they share one or more gatelocks.


<!SLIDE>
a basic example

    @@@ cpp
        mutex A, B, C, D;
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    A; B; C; D;
})


<!SLIDE>
.notes It is NOT redundant to put `A` in the set of gatelocks, as is seen in
the next slide.

a basic example

    @@@ cpp
        mutex A, B, C, D;
        thread t1([&] {
            A.lock();
            B.lock();
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    A; B; C; D;
    A->B [label="t1 holding {A}"];
})


<!SLIDE>
.notes Here, we can see that putting `B` in the set of gatelocks when
acquiring `C` is not redundant, because the edge from `A` to `C` needs it.

a basic example

    @@@ cpp
        mutex A, B, C, D;
        thread t1([&] {
            A.lock();
            B.lock();
            C.lock();
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    A; B; C; D;
    A->B [label="t1 holding {A}"];
    A->C [label="t1 holding {A, B}"];
    B->C [label="t1 holding {A, B}"];
})


<!SLIDE>
a basic example

    @@@ cpp
        mutex A, B, C, D;
        thread t1([&] {
            A.lock();
            B.lock();
            C.lock();
        });

        thread t2([&] {

        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {

})


<!SLIDE>
.notes Since t1 and t2 will never run in parallel (t1 is joined before t2 is
created), they obviously can't deadlock

however, consider this situation:

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
                B.lock();
                B.unlock();
            A.unlock();
        });
        t1.join();

        thread t2([&] {
            B.lock();
                A.lock();
                A.unlock();
            B.unlock();
        });
