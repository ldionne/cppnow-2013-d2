<!SLIDE>
# false positives


<!SLIDE>
.notes Since there is only one thread involved, a deadlock can't possibly happen. Actually, the generalization is that all the code in this thread is implicitly serialized (because it is run in a single thread of execution). Therefore, there exists an implicit happens-before relationship between the statements. Note that a deadlock could still happen if a non-recursive lock was locked recursively by a thread. However, whether such a deadlock happens depends on whether the code path leading to it is taken. In other words, it is deterministic as far as thread scheduling is concerned. If the code path is taken, the deadlock is 100% to happen. Otherwise, the deadlock won't happen and we won't detect anything anyway since the code path was _not_ taken (and we're doing dynamic analysis).
# single thread

    @@@ cpp
        mutex A, B;
        thread t1([&] {
            A.lock();
                B.lock();
                B.unlock();
            A.unlock();

            B.lock();
                A.lock();
                A.unlock();
            B.unlock();
        });


<!SLIDE>
.notes the deadlock may never happen because G has to be held by both threads in order to enter the dangerous section of the code
# gatelock

    @@@ cpp
        mutex A, B, G;
        thread t1([&] {
            G.lock();
                A.lock();
                    B.lock();
                    B.unlock();
                A.unlock();
            G.lock();
        });

        thread t2([&] {
            G.lock();
                B.lock();
                    A.lock();
                    A.unlock();
                B.unlock();
            G.lock();
        });


<!SLIDE>
.notes since t1 and t2 will never run in parallel (t1 is joined before t2 is created), they obviously can't deadlock
# synchronized thread

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
