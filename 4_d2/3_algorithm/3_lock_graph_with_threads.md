<!SLIDE>
.notes If a cycle contains two edges labelled with the same thread, it is
ignored because the represented deadlock would require code in the same
thread to run concurrently, which is impossible. This is effectively a
special case of the happens-before relationship.

Let's augment the lock graph by labelling each edge with the thread that
caused that edge to be added.

We will ignore cycles containing two edges labelled with the same thread.


<!SLIDE>
# Example \#1
No labels, no edges, like the basic graph.

    @@@ cpp
        mutex A, B;
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph { A; B; })


<!SLIDE>
# Example \#1
When an edge is added, we label it with the thread that caused its addition.

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
            B.lock();
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    A->B [label=t1];
})


<!SLIDE>
# Example \#1
We add a parallel edge if the label on it is different from that of existing
edges.

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
            B.lock();
        });

        thread t2([&] {
            A.lock();
            B.lock();
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    A->B [label=t1];
    A->B [label=t2];
})


<!SLIDE>
Now consider the previous graph with a false positive:

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
                B.lock();
                B.unlock();
            A.unlock();

            B.lock();
            A.lock();
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    A->B [label=t1];
    B->A [label=t1];
})

The false positive is no more; the cycle is within a single thread and is
ignored.


<!SLIDE>
.notes One cycle is ignored because it is single threaded, and the other cycle
is not ignored, which is good because it represents a potential deadlock.

And cycles still represent potential deadlocks (if you trust me).

    @@@ cpp
        mutex A, B, C;
        thread t1([&] {
            A.lock();
            B.lock();
        });

        thread t2([&] {
            B.lock();
                C.lock();
                C.unlock();
            B.unlock();

            C.lock();
            A.lock();
        });

        thread t3([&] {
            C.lock();
            A.lock();
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    A->B [label=t1];
    B->C [label=t2];
    C->A [label=t2];
    C->A [label=t3];
})


<!SLIDE>
.notes The deadlock may never happen because G has to be held by both threads
in order to enter the dangerous section of the code.

However, consider this situation:

        @@@ cpp
            mutex A, B, G;
            thread t1([&] {
                G.lock();
                A.lock();
                B.lock();
            });

            thread t2([&] {
                G.lock();
                B.lock();
                A.lock();
            });


<!SLIDE>
Yielding this graph:

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    rankdir=LR;
    G->A [label=t1];
    G->B [label=t1];
    A->B [label=t1];
    G->B [label=t2];
    G->A [label=t2];
    B->A [label=t2];
})

We incorrectly find a possible deadlock; in order for the deadlock to happen,
both threads must be holding `G`, which can't happen at the same time. We say
that `G` is a gatelock protecting that cycle.
