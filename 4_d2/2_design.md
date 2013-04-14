<!SLIDE subsection>
# Design


<!SLIDE>
## Concepts from Boost.Thread

    @@@ cpp
        boost::BasicLockable
        boost::Lockable
        boost::TimedLockable


<!SLIDE>
## High level concept based API

    @@@ cpp
        d2::basic_lockable
        d2::lockable
        d2::timed_lockable
        d2::trackable_thread

.notes Each wrapper has a mixin counterpart to accomodate most use cases.
Also, trackable_thread is for standard conforming thread implementations.


<!SLIDE>
## Recursive flavors

    @@@ cpp
        d2::recursive_basic_lockable
        d2::recursive_lockable
        d2::recursive_timed_lockable

.notes Each wrapper has a recursive counterpart that is a simple shortcut for
the longer version.


<!SLIDE>
## Low level C API (bindings someone?)

    @@@ c
        d2_notify_acquire(thread, lock)
        d2_notify_release(thread, lock)
        d2_notify_start(parent, child)
        d2_notify_join(parent, child)


<!SLIDE>
## Event generation and dispatch to the filesystem

    @@@ cpp
        dispatch(core::acquire(thread, lock))
        dispatch(core::release(thread, lock))
        dispatch(core::start(parent, child))
        dispatch(core::join(parent, child))


<!SLIDE>
## Organization of events inside a directory

![Example repository](example_repository.png)


<!SLIDE #mumbo_jumbo commandline>
## Mumbo jumbo on disk

    $ cat my_program/1    # thread 1
    22 serialization::archive 10 0 0 4 0 0 0 0 1 0 0 2 0 0 0 0 0 1 0 0 2 0 0 0 0 0 0 0 1 3 0 0 0 1 4 0 0 0 1 5 0 0 0 1 6 0 0 0 1 7 0 0 0 1 8 0 0 0 1 9 0 0 0 1 10 0 0 0 1 11 0 0 0 1 12 0 0 0 1 13 0 0 0 1 14 0 0 0 1 15 0 0 0 1 16 0 0 0 1 17 0 0 0 1 18 0 0 0 1 19 0 0 0 1 20 0 0 0 1 21 0 0 0 1 22 0 0 0 1 23 0 0 0 1 24 0 0 0 1 25 0 0 0 1 26 0 0 0 1 27 0 0 0 1 28 0 0 0 1 29 0 0 0 1 30 0 0 0 1 31 0 0 0 1 32 0 0 0 1 33 0 0 0 1 34 0 0 0 1 35 0 0 0 1 36 0 0 0 1 37 0 0 0 1 38 0 0 0 1 39 0 0 0 1 40 0 0 0 1 41 0 0 0 1 42 0 0 0 1 43 0 0 0 1 44 0 0 0 1 45 0 0 0 1 46 0 0 0 1 47 0 0 0 1 48 0 0 0 1 49 0 0 0 1 50 0 0 0 1 51 0 0 0 1 52 0 0 0 1 53 0 0 0 1 54 0 0 0 1 55 0 0 0 1 56 0 0 0 1 57 0 0 0 1 58 0 0 0 1 59 0 0 0 1 60 0 0 0 1 61 0 0 0 1 62 0 0 0 1 63 0 0 0 1 64 0 0 0 1 65 0 0 0 1 66 0 0 0 1 67 0 0 0 1 68 0 0 0 1 69 0 0 0 1 70 0 0 0 1 71 0 0 0 1 72 0 0 0 1 73 0 0 0 1 74 0 0 0 1 75 0 0 0 1 76 0 0 0 1 77 0 0 0 1 78 0 0 0 1 79 0 0 0 1 80 0 0 0 1 81 0 0 0 1 82 0 0 0 1 83 0 0 0 1 84 0 0 0 1 85 0 0 0 1 86 0 0 0 1 87 0 0 0 1 88 0 0 0 1 89 0 0 0 1 90 0 0 0 1 91 0 0 0 1 92 0 0 0 1 93 0 0 0 1 94 0 0 0 1 95 0 0 0 1 96 0 0 0 1 97 0 0 0 1 98 0 0 0 1 99 0 0 0 1 100 0 0 0 1 101 0 0 1 0 0 1 101 0 0 1 1 100 0 0 1 1 99 0 0 1 1 98 0 0 1 1 97 0 0 1 1 96 0 0 1 1 95 0 0 1 1 94 0 0 1 1 93 0 0 1 1 92 0 0 1 1 91 0 0 1 1 90 0 0 1 1 89 0 0 1 1 88 0 0 1 1 87 0 0 1 1 86 0 0 1 1 85 0 0 1 1 84 0 0 1 1 83 0 0 1 1 82 0 0 1 1 81 0 0 1 1 80 0 0 1 1 79 0 0 1 1 78 0 0 1 1 77 0 0 1 1 76 0 0 1 1 75 0 0 1 1 74 0 0 1 1 73 0 0 1 1 72 0 0 1 1 71 0 0 1 1 70 0 0 1 1 69 0 0 1 1 68 0 0 1 1 67 0 0 1 1 66 0 0 1 1 65 0 0 1 1 64 0 0 1 1 63 0 0 1 1 62 0 0 1 1 61 0 0 1 1 60 0 0 1 1 59 0 0 1 1 58 0 0 1 1 57 0 0 1 1 56 0 0 1 1 55 0 0 1 1 54 0 0 1 1 53 0 0 1 1 52 0 0 1 1 51 0 0 1 1 50 0 0 1 1 49 0 0 1 1 48 0 0 1 1 47 0 0 1 1 46 0 0 1 1 45 0 0 1 1 44 0 0 1 1 43 0 0 1 1 42 0 0 1 1 41 0 0 1 1 40 0 0 1 1 39 0 0 1 1 38 0 0 1 1 37 0 0 1 1 36 0 0 1 1 35 0 0 1 1 34 0 0 1 1 33 0 0 1 1 32 0 0 1 1 31 0 0 1 1 30 0 0 1 1 29 0 0 1 1 28 0 0 1 1 27 0 0 1 1 26 0 0 1 1 25 0 0 1 1 24 0 0 1 1 23 0 0 1 1 22 0 0 1 1 21 0 0 1 1 20 0 0 1 1 19 0 0 1 1 18 0 0 1 1 17 0 0 1 1 16 0 0 1 1 15 0 0 1 1 14 0 0 1 1 13 0 0 1 1 12 0 0 1 1 11 0 0 1 1 10 0 0 1 1 9 0 0 1 1 8 0 0 1 1 7 0 0 1 1 6 0 0 1 1 5 0 0 1 1 4 0 0 1 1 3 0 0 1 1 2 0 0


<!SLIDE #d2_output_all commandline>
.notes d2tool loads the events, constructs the graphs and performs the analysis.
Also mention that this output was cropped and edited a bit for clarity.

## `d2tool` speaks that mumbo jumbo

    $ d2tool --analyze myprogram
    in thread #2 started at [no location information]:
    holds object #1 acquired at
    scenario_ABBA            main::$_1::operator()() const
    ...

    tries to acquire object #0 at
    scenario_ABBA            main::$_1::operator()() const
    ...

    in thread #1 started at [no location information]:
    holds object #0 acquired at
    scenario_ABBA            main::$_0::operator()() const
    ...

    tries to acquire object #1 at
    scenario_ABBA            main::$_0::operator()() const
    ...


<!SLIDE #d2_output commandline>
## `d2tool` speaks that mumbo jumbo

    $ d2tool --analyze myprogram
    in thread #2 started at [no location information]:
    holds object #1 acquired at
    [...]/scenario_ABBA            main::$_1::operator()() const
    [...]/scenario_ABBA            boost::detail::function::void_function_obj_invoker0<main::$_1, void>::invoke(boost::detail::function::function_buffer&)
    [...]/scenario_ABBA            boost::function0<void>::operator()() const
    [...]/scenario_ABBA            d2mock::thread::impl::impl(boost::function<void ()> const&)::'lambda'()::operator()() const
    [...]/scenario_ABBA            d2::thread_function<d2mock::thread::impl::impl(boost::function<void ()> const&)::'lambda'()>::result<d2::thread_function<d2mock::thread::impl::impl(boost::function<void ()> const&)::'lambda'()> ()>::type d2::thread_function<d2mock::thread::impl::impl(boost::function<void ()> const&)::'lambda'()>::operator()<>()
    [...]/scenario_ABBA            boost::detail::thread_data<d2::thread_function<d2mock::thread::impl::impl(boost::function<void ()> const&)::'lambda'()> >::run()
    [...]/libboost_thread-mt.dylib thread_proxy
    [...]/libsystem_c.dylib        _pthread_start
    [...]/libsystem_c.dylib        thread_start
