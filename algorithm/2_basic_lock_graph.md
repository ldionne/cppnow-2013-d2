<!SLIDE>
let's start with a basic lock graph where an edge from A to B means B was acquired by some thread holding A


<!-- basic lock graph: A -> B graph created iteratively -->
<!SLIDE>
    @@@ cpp
        mutex A, B;
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph { A; B; })

.notes Two nodes are created because we created two locks, but no edges are created because we did not lock anything.


<!SLIDE>
no edge is created; the main thread does not hold anything when it acquires A

    @@@ cpp
        mutex A, B;
        A.lock();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph { A; B; })


<!SLIDE>
the main thread holds A when it acquires B; we add an edge from A to B

    @@@ cpp
        mutex A, B;
        A.lock();
            B.lock();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph { A->B; })


<!SLIDE>
the graph is not modified on releases

    @@@ cpp
        mutex A, B;
        A.lock();
            B.lock();
            B.unlock();
        A.unlock();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph { A->B; })


<!SLIDE>
we don't add redundant edges

    @@@ cpp
        mutex A, B;
        A.lock();
            B.lock();
            B.unlock();
        A.unlock();

        A.lock();
            B.lock();
            B.unlock();
        A.unlock();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph { A -> B; })

.notes Since there is already an edge from A to B in the graph, we don't add it redundantly if a thread locks B again while holding A.


<!SLIDE>
to clarify any ambiguities, consider a graph with more vertices

    @@@ cpp
        mutex A, B, C, D;
        A.lock();
        B.lock();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    A; B; C; D;
    A->B;
})


<!SLIDE>
we're really computing the transitive closure of the "is held by a thread when acquiring X"

    @@@ cpp
        mutex A, B, C, D;
        A.lock();
        B.lock();
        C.lock();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    A; B; C; D;
    A->B;
    A->C;
    B->C;
})


<!SLIDE>
    @@@ cpp
        mutex A, B, C, D;
        A.lock();
        B.lock();
        C.lock();
        D.lock();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    A->B;
    A->C;
    B->C;
    A->D;
    B->D;
    C->D;
})


<!SLIDE>
let's examine a graph representing a potential deadlock

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
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    A->B;
    B->A;
})
