<!SLIDE subsection>
# Our target: lock order inconsistencies


<!SLIDE>
## Example \#1

    @@@ cpp
        mutex A, B;
        thread t1([&] { // gotta <3 lambdas
            A.lock();
                B.lock();
        });

        thread t2([&] {
            B.lock();
                A.lock();
        });


<!SLIDE smbullets incremental>
## Consider what happens if...

* `t1` starts and locks `A`
* `t1` is preempted by the OS
* `t2` starts and locks `B`
* `t2` tries to lock `A`
* `t1` is woken up and tries to lock `B`


<!SLIDE>
## Example \#2

    @@@ cpp
        mutex A, B, C;
        thread t1([&] {
            A.lock();
                B.lock();
        });

        thread t2([&] {
            B.lock();
                C.lock();
        });

        thread t3([&] {
            C.lock();
                A.lock();
        });


<!SLIDE>
# Same game, larger headache


<!SLIDE>
# Why are they so vicious?


<!SLIDE>
## They are non deterministic bugs


<!SLIDE>
.notes Very few variations in thread scheduling usually happens for different
runs of the same code. For this reason, odds are that rare deadlocks still
make it to production and only happen under "extreme" conditions.

## Rare ones will probably not be caught by unit tests


<!SLIDE>
## They are difficult to reproduce


<!SLIDE>
## Finding them requires thinking about parallel executions, which is difficult


<!SLIDE>
# The real deal is to find them before they happen
