<!SLIDE>
# The happens-before relation


<!SLIDE>
We can implement this relation by associating an identifier to segments of the
code that are separated by the start or join of a thread.


<!SLIDE>
When a thread starts another thread, both the parent and the child threads are
assigned new segment identifiers.


<!SLIDE>
When a thread joins another thread, the parent thread continues executing with
a new segment identifier.


<!SLIDE>
By drawing directed edges between the segments, we end up with a graph where
node `v` is reachable from node `u` iff `u` happens before `v`.


<!SLIDE>
If two acquires do not happen before the other, then they must surely happen
in parallel.


<!SLIDE graph_example>
## Example \#1

    @@@ cpp
        thread t1([] {});
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
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


<!SLIDE graph_example>
## Example \#1

    @@@ cpp
        thread t1([] {});
        thread t2([] {
            // ...
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
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
        main0 -> t10;
        t10 [label = 2];
    }
    subgraph cluster_t2 {
        label = "thread 2";
        main1 -> t20;
        t20 [label = 4];
    }
})


<!SLIDE graph_example>
## Example \#1

    @@@ cpp
        thread t1([] {});
        thread t2([] {
            thread t3([] {});
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
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
        main0 -> t10;
        t10 [label = 2];
    }
    subgraph cluster_t2 {
        label = "thread 2";
        main1 -> t20 -> t21;
        t20 [label = 4];
        t21 [label = 5];
    }
    subgraph cluster_t3 {
        label = "thread 3";
        t20 -> t30;
        t30 [label = 6];
    }
})


<!SLIDE graph_example>
## Example \#1

    @@@ cpp
        thread t1([] {});
        thread t2([] {
            thread t3([] {});
            t3.join();
        });
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
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
        main0 -> t10;
        t10 [label = 2];
    }
    subgraph cluster_t2 {
        label = "thread 2";
        main1 -> t20 -> t21 -> t22;
        t20 [label = 4];
        t21 [label = 5];
        t22 [label = 7];
    }
    subgraph cluster_t3 {
        label = "thread 3";
        t20 -> t30 -> t22;
        t30 [label = 6];
    }
})


<!SLIDE graph_example>
## Example \#1

    @@@ cpp
        thread t1([] {});
        thread t2([] {
            thread t3([] {});
            t3.join();
        });
        t1.join();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    graph [splines = ortho];
    subgraph cluster_main {
        label = "main";
        main0 -> main1 -> main2 -> main3;
        main0 [label = 0];
        main1 [label = 1];
        main2 [label = 3];
        main3 [label = 8];
    }
    subgraph cluster_t1 {
        label = "thread 1";
        main0 -> t10 -> main3;
        t10 [label = 2];
    }
    subgraph cluster_t2 {
        label = "thread 2";
        main1 -> t20 -> t21 -> t22;
        t20 [label = 4];
        t21 [label = 5];
        t22 [label = 7];
    }
    subgraph cluster_t3 {
        label = "thread 3";
        t20 -> t30 -> t22;
        t30 [label = 6];
    }
})


<!SLIDE graph_example>
## Example \#1

    @@@ cpp
        thread t1([] {});
        thread t2([] {
            thread t3([] {});
            t3.join();
        });
        t1.join();
        t2.join();
![](https://chart.googleapis.com/chart?cht=gv&chl=digraph {
    graph [splines = ortho];
    subgraph cluster_main {
        label = "main";
        main0 -> main1 -> main2 -> main3 -> main4;
        main0 [label = 0];
        main1 [label = 1];
        main2 [label = 3];
        main3 [label = 8];
        main4 [label = 9];
    }
    subgraph cluster_t1 {
        label = "thread 1";
        main0 -> t10 -> main3;
        t10 [label = 2];
    }
    subgraph cluster_t2 {
        label = "thread 2";
        main1 -> t20 -> t21 -> t22 -> main4;
        t20 [label = 4];
        t21 [label = 5];
        t22 [label = 7];
    }
    subgraph cluster_t3 {
        label = "thread 3";
        t20 -> t30 -> t22;
        t30 [label = 6];
    }
})
