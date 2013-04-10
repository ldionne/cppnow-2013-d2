<!SLIDE>
now, let's augment the lock graph by recording the segment in which an acquire
is made

a cycle is not valid if any edge in the cycle `happens_before` another edge in
the cycle


<!SLIDE>
let's go back to our false positive

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


<!SLIDE>

    @@@ cpp
        mutex A, B;

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    label = "segmentation graph";
    graph [splines = ortho];
    subgraph cluster_main {
        label = "main";
        main0 [label = 0];
    }
})

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    label = "lock graph";
    A; B;
})


<!SLIDE>

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
                B.lock();
                B.unlock();
            A.unlock();
        });

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    label = "segmentation graph";
    graph [splines = ortho];
    subgraph cluster_main {
        label = "main";
        main0 -> main1;
        main0 [label = 0];
        main1 [label = 1];
    }
    subgraph cluster_t1 {
        label = "thread 1";
        main0 -> t10;
        t10 [label = 2];
    }
})

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    label = "lock graph";
    A; B;
    A -> B [label = "t1 acquires A in segment 2 while holding {A taken in segment 2}"];
})


<!SLIDE>

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
                B.lock();
                B.unlock();
            A.unlock();
        });
        t1.join();

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    label = "segmentation graph";
    graph [splines = ortho];
    subgraph cluster_main {
        label = "main";
        main0 -> main1 -> main2;
        main0 [label = 0];
        main1 [label = 1];
        main2 [label = 3];
    }
    subgraph cluster_t1 {
        label = "thread 1";
        main0 -> t10 -> main2;
        t10 [label = 2];
    }
})

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    label = "lock graph";
    A; B;
    A -> B [label = "t1 acquires A in segment 2 while holding {A taken in segment 2}"];
})


<!SLIDE>

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

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    label = "segmentation graph";
    graph [splines = ortho];
    subgraph cluster_main {
        label = "main";
        main0 -> main1 -> main2 -> main3 -> main4;
        main0 [label = 0];
        main1 [label = 1];
        main2 [label = 3];
        main3 [label = 5];
        main4 [label = 6];
    }
    subgraph cluster_t1 {
        label = "thread 1";
        main0 -> t10 -> main2;
        t10 [label = 2];
    }
    subgraph cluster_t2 {
        label = "thread 2";
        main2 -> t20 -> main4;
        t20 [label = 4];
    }
})

![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    label = "lock graph";
    A; B;
    A -> B [label = "t1 acquires B in segment 2 while holding {A taken in segment 2}"];
    B -> A [label = "t2 acquires A in segment 4 while holding {B taken in segment 4}"];
})


<!SLIDE>
the cycle will be ignored because segment 2 happens before segment 4
