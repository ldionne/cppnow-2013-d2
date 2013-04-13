<!SLIDE subsection>
# Using `d2`


<!SLIDE>
# Tracking arbitrary synchronization objects

    @@@ cpp
        class mutex {
        public:
            void lock();
            void unlock();
        };


<!SLIDE>
# First way

    @@@ cpp
        class untracked_mutex {
        public:
            void lock();
            void unlock();
        };

        typedef d2::basic_lockable<untracked_mutex> mutex;


<!SLIDE>
# Second way

    @@@ cpp
        class mutex : public d2::basic_lockable_mixin<mutex> {
            friend class d2::basic_lockable_mixin<mutex>;
            void lock_impl();
            void unlock_impl();
        };

.notes explain why this way exists (nested typedef representing the mutex type)


<!SLIDE>
# Third way (if you really have to)

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
# Tracking standard conforming threads

    @@@ cpp
        class thread {
            // ...
        };


<!SLIDE>
# First way

    @@@ cpp
        class untracked_thread {
            // ...
        };
        typedef d2::trackable_thread<untracked_thread> thread;


<!SLIDE>
# Second way

    @@@ cpp
        class thread : public d2::trackable_thread_mixin<thread> {
            friend class d2::trackable_thread_mixin<thread>;
            void join_impl();
            void detach_impl();

        public:
            template <typename F, typename ...Args>
            explicit thread(F&& f, Args&& ...args) {
                d2::thread_function<F> f_ =
                            this->get_thread_function(boost::forward<F>(f));
                // use f_ normally
            }
        };


<!SLIDE>
# Tracking threads with arbitrary implementations

    @@@ cpp
        class thread {
            d2::thread_lifetime lifetime_;
            // ...
        };


<!SLIDE>
# Thread birth

    @@@ cpp
        template <typename Function, typename ...Args>
        void start(Function&& f, Args&& ...args) {
            lifetime_.about_to_start();
            d2::thread_function<Function> f_(lifetime_, f);
            // start the thread with f_ instead of f
        }


<!SLIDE>
# Letting go

    @@@ cpp
        void detach() {
            // ...
            lifetime_.just_detached();
        }


<!SLIDE>
# Thread death

    @@@ cpp
        void join() {
            // ...
            lifetime_.just_joined();
        }


<!SLIDE commandline>
.notes Right now, the integration is only with pthreads because I lack access
to a windows machine.

# Everything is easier with Boost

    $ cd ${boost_root}

    $ patch -p1 < ${d2_root}/integrations/boost/1_53_00.diff

    $ ./bjam -j64 # :)
