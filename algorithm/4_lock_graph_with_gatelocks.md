<!SLIDE>
this time, we will augment the edge labels to record the set of locks held by
the thread causing an edge to be added to the lock graph.

a cycle is not valid if the gatelock sets of any two edges in the cycle
intersect, i.e. if they share one or more gatelocks.


<!SLIDE>
a basic example

    @@@ cpp
        mutex A, B, C, D;
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    A; B; C; D;
})


<!SLIDE>
.notes It is NOT redundant to put `A` in the set of gatelocks, as can be seen
in the next slide.

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
            D.lock();
            A.lock();
            B.lock();
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    A->B [label="t1 holding {A}"];
    A->C [label="t1 holding {A, B}"];
    B->C [label="t1 holding {A, B}"];
    D->A [label="t2 holding {D}"];
    D->B [label="t2 holding {D, A}"];
    A->B [label="t2 holding {D, A}"];
})


<!SLIDE>
now consider the previous graph with a false positive

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    G->A [label="t1 holding {G}"];
    G->B [label="t1 holding {G, A}"];
    A->B [label="t1 holding {G, A}"];
    G->B [label="t2 holding {G}"];
    G->A [label="t2 holding {G, B}"];
    B->A [label="t2 holding {G, B}"];
})

as expected, the false positive is inhibited by the intersecting sets of
gatelocks


<!SLIDE>
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
        t2.join();


<!SLIDE>
yielding this graph

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    A->B [label="t1 holding {A}"];
    B->A [label="t2 holding {B}"];
})

we incorrectly detect a deadlock even though `t1` and `t2` will never run
in parallel, because `t1` is joined before `t2` starts. we will say that
`t1` happens before `t2`.
