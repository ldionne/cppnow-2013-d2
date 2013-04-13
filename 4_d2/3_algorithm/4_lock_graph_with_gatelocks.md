<!SLIDE>
This time, we will augment the edge labels to record the set of locks held by
the thread causing an edge to be added to the lock graph.

A cycle is not valid if the gatelock sets of any two edges in the cycle
intersect, i.e. if they share one or more gatelocks.


<!SLIDE>
# Example \#1

    @@@ cpp
        mutex A, B, C, D;
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph { A; B; C; D; })


<!SLIDE>
.notes It is NOT redundant to put `A` in the set of gatelocks, as can be seen
in the next slide.

# Example \#1

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

# Example \#1

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
# Example \#1

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
Now consider the previous graph with a false positive:

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    G->A [label="t1 holding {G}"];
    G->B [label="t1 holding {G, A}"];
    A->B [label="t1 holding {G, A}"];
    G->B [label="t2 holding {G}"];
    G->A [label="t2 holding {G, B}"];
    B->A [label="t2 holding {G, B}"];
})

As expected, the false positive is inhibited by the intersecting sets of
gatelocks.


<!SLIDE>
However, consider this situation:

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
            B.lock();
        });
        t1.join();

        thread t2([&] {
            B.lock();
            A.lock();
        });
        t2.join();


<!SLIDE>
Yielding this graph:

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    A->B [label="t1 holding {A}"];
    B->A [label="t2 holding {B}"];
})

We incorrectly detect a deadlock even though `t1` and `t2` will never run
in parallel, because `t1` is joined before `t2` starts. We will say that
`t1` happens before `t2`.
