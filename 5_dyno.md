<!SLIDE subsection small>
# `dyno`: a dynamic analysis library


<!SLIDE>
## This is __very__ experimental, so we won't go in depth


<!SLIDE smbullets>
# Idea

* Events are generated by a program and recorded
* Custom actions can be bound to these events
* Events can be loaded conveniently to analyze a program trace


<!SLIDE small>
## First, define an event

    @@@ cpp
    namespace tags {
      struct acquire;
      struct lock_id;
    }

    typedef event<tags::acquire,
              records<call_stack>,
              records<thread_id>,
              records<
               custom_info<tags::lock_id, unsigned>
              >
            > acquire_event;


<!SLIDE small>
## Then, bundle the events into a `framework`

    @@@ cpp
    typedef framework<
              events<
                acquire_event, release_event,
                start_event, join_event
              >,
              backend<save_on_filesystem>
            > d2_framework_t;

    static d2_framework_t d2_framework;


<!SLIDE small>
## Bind actions to events as wanted

    @@@ cpp
    dyno::on<tags::acquire>(d2_framework,
        [](acquire_event e) {
          // whatever
        });


<!SLIDE small>
## Generate events in your code

    @@@ cpp
    struct mutex {
      void lock() {
        dyno::generate<tags::acquire>(d2_framework, lock_id_);
      }

    private:
      unsigned lock_id_;
    };


<!SLIDE small>
## Load events from a source to perform your custom analysis without hassle

    @@@ cpp
    struct populate_lock_graph {
      void operator()(acquire_event e) const;
      void operator()(release_event e) const;

      template <typename AnyOtherEvent>
      void operator()(AnyOtherEvent) const;
    };

    dyno::load_events("some_directory", populate_lock_graph());


<!SLIDE smbullets>
.notes Explain how the project was started by witnessing that much of the
       code from d2 could be generally useful for dynamic analysis.

# Use cases

* Simplifying the implementation of `d2`
* Benchmarking and checking memory allocations
* Gathering statistics during program execution
