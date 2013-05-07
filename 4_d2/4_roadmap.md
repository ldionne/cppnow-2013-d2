<!SLIDE smbullets incremental>
# Limitations and drawbacks

* Requires modifying existing code
* No integration with an IDE
* Meaningful diagnostics is difficult and possibly impossible in release builds
* Current implementation is immature


<!SLIDE smbullets incremental>
# Roadmap
* Support a wider set of synchronization primitives
* Provide better diagnostics
* Support detached threads
* Consider gatelocks held by parent threads to further reduce false positives
* Provide integration with more libraries
