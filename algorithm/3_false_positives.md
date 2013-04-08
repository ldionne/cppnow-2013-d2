<!SLIDE>
# false positives


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
