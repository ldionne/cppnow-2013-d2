<!SLIDE smbullets incremental>
# Limitations and drawbacks

* Requires modifying existing code
* No integration with an IDE
* Meaningful diagnostics is difficult and possibly impossible in release builds
* Current implementation is immature


<!SLIDE smbullets incremental>
# Roadmap
* Provide better diagnostics
* Support read/write locks
* Support other synchronization primitives
* Support detached threads
* Support lock upgrading/downgrading
* Consider gatelocks held by parent threads to further reduce false positives
* Provide integration with more libraries
