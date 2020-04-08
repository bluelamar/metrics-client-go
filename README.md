# metrics-client-go
A metrics client API that can send metric records to configurable backend target

The client provides API for an application to update statistics during its runtime.
These updates are reported at end of time windows. Whereupon the stats are sent to the target server or file.

The backend targets are configurable and the package comes with builtin targets for directory and Cassandra.
However, the user may specify their own backend target.

There are default common metrics supported via simple API.
1. memory usage
2. disk usage
3. resource usage
