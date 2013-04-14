<!SLIDE subsection>
# Using `d2`


<!SLIDE smaller>
# Tracking arbitrary synchronization objects

    @@@ cpp
        class mutex {
        public:
            void lock();
            void unlock();
        };


<!SLIDE smaller>
# First way

    @@@ cpp
        class untracked_mutex {
        public:
            void lock();
            void unlock();
        };

        typedef d2::basic_lockable<untracked_mutex> mutex;


<!SLIDE smaller>
# Second way

    @@@ cpp
        class mutex : public d2::basic_lockable_mixin<mutex> {
            friend class d2::basic_lockable_mixin<mutex>;
            void lock_impl() {
                // normal code
            }

            void unlock_impl() {
                // normal code
            }
        };

.notes explain why this way exists (nested typedef representing the mutex type)


<!SLIDE smaller>
# Third way (if you really have to)

    @@@ cpp
        class mutex
            : d2::trackable_sync_object<d2::non_recursive>
        {
        public:
            void lock() {
                // normal code
                this->notify_lock();
            }

            void unlock() {
                // normal code
                this->notify_unlock();
            }
        };


<!SLIDE smaller>
# Tracking standard conforming threads

    @@@ cpp
        class thread {
            // ...
        };


<!SLIDE smaller>
# First way

    @@@ cpp
        class untracked_thread {
            // ...
        };
        typedef d2::trackable_thread<untracked_thread> thread;


<!SLIDE smaller>
# Second way

    @@@ cpp
        class thread
            : public d2::trackable_thread_mixin<thread>
        {
            friend class d2::trackable_thread_mixin<thread>;
            // ...


<!SLIDE smaller>
# Starting

    @@@ cpp
        public:
            template <typename F, typename ...Args>
            explicit thread(F&& f, Args&& ...args) {
                typedef d2::thread_function<F> F_;
                F_ f_ = this->get_thread_function(
                                boost::forward<F>(f));

                // normal code using F_ and f_
            }


<!SLIDE smaller>
# Detaching

    @@@ cpp
        private:
            void detach_impl() {
                // normal code
            }


<!SLIDE smaller>
# Joining

    @@@ cpp
        private:
            void join_impl() {
                // normal code
            }


<!SLIDE smaller>
# Don't forget these or you'll be sorry

    @@@ cpp
        public:
            thread(thread&& other)
                : trackable_thread_mixin_(boost::move(other))
            { }

            thread& operator=(thread&& other) {
                trackable_thread_mixin_::operator=(
                                        boost::move(other));
                // ...
            }
        };


<!SLIDE smaller>
# Tracking threads with arbitrary implementations

    @@@ cpp
        class thread {
            d2::thread_lifetime lifetime_;
            // ...
        };


<!SLIDE smaller>
# Starting

    @@@ cpp
        template <typename F, typename ...Args>
        void start(F&& f, Args&& ...args) {
            lifetime_.about_to_start();
            d2::thread_function<F> f_(lifetime_, f);
            // normal code with f_
        }


<!SLIDE smaller>
# Detaching

    @@@ cpp
        void detach() {
            // normal code
            lifetime_.just_detached();
        }


<!SLIDE smaller>
# Joining

    @@@ cpp
        void join() {
            // normal code
            lifetime_.just_joined();
        }


<!SLIDE commandline>
.notes Right now, the integration is only with pthreads because I lack access
to a windows machine.

## Everything is easier with Boost

    $ cd ${boost_root}

    $ patch -p1 < ${d2_root}/integrations/boost/1_53_00.diff

    $ ./bjam -j64 # :)
