<!SLIDE subsection>
# Using `d2`


<!SLIDE>
# tracking arbitrary synchronization objects

    @@@ cpp
        class mutex {
        public:
            void lock();
            void unlock();
        };


<!SLIDE>
# first way

    @@@ cpp
        class untracked_mutex {
        public:
            void lock();
            void unlock();
        };

        typedef d2::basic_lockable<untracked_mutex> mutex;


<!SLIDE>
# second way

    @@@ cpp
        class mutex : public d2::basic_lockable_mixin<mutex> {
            friend class d2::basic_lockable_mixin<mutex>;
            void lock_impl();
            void unlock_impl();
        };

.notes explain why this way exists (nested typedef representing the mutex type)


<!SLIDE>
# 3rd way (if you really have to)

    @@@ cpp
        class mutex : private d2::trackable_sync_object<d2::non_recursive> {
        public:
            void lock() {
                // ...
                this->notify_lock();
            }

            void unlock() {
                // ...
                this->notify_unlock();
            }
        };


<!SLIDE>
# tracking standard conforming threads

    @@@ cpp
        class untracked_thread {
            // ...
        };
        typedef d2::trackable_thread<untracked_thread> thread;


<!SLIDE>
# tracking threads with arbitrary implementations

    @@@ cpp
        class thread {
            d2::thread_lifetime lifetime_;

            // ...


<!SLIDE>
# thread birth

    @@@ cpp
        template <typename Function, typename ...Args>
        void start(Function&& f, Args&& ...args) {
            lifetime_.about_to_start();
            d2::thread_function<Function> f_(lifetime_, f);
            // start the thread with f_ instead of f
        }


<!SLIDE>
# letting go

    @@@ cpp
        void detach() {
            // ...
            lifetime_.just_detached();
        }


<!SLIDE>
# thread death

    @@@ cpp
        void join() {
            // ...
            lifetime_.just_joined();
        }


<!SLIDE commandline>
# everything is easier with Boost

    $ cd ${boost_root}

    $ patch -p1 < ${d2_root}/integrations/boost/1_53_00.diff

    $ ./bjam -j64 # :)
