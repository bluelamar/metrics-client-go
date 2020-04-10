# metrics-client-go
A metrics client API that can send metric records to configurable backend target

This client provides API for an application to update statistics during its runtime.
These updates are reported at end of configurable time windows. Whereupon the stats are sent to the target server or file.

The backend targets are configurable and the package comes with builtin targets for directory and Cassandra.
However, the user may specify their own backend target.

This provides a generic API for user defined metrics, as well as some API commonly used by applications.
The generic API are:
1. func UpdateStat(name, value)

Common metrics are supported via simple API:
1. memory usage: func UpdateMemoryUsage()
2. disk usage: func UpdateDiskUsage()
3. resource usage: func UpdateResourceUsage()
4. timing, latency: func StartTimerNanos(), func StartTimerMillis(), func EndTimer(*Timer)

## Types of Metrics Supported

**Accumulator**: accumulates sum of all values added during the time slice
- ex: number times a method called
- ex: total exec millis a method has run

**Average**: average is calculated for all values added during the time slice
- ex: method exec time millis

**Percentile**: percentage is calculated for all values added during the time slice
- ex: requests timed, percentage of those below a limit

**Snapshot**: value recorded at a moment in time
- ex: memory usage of the server

## Example Usage

```
type Logger interface {
  LogError(msg string)
}
type LogHandler struct {
  prefix string
}

func(fh *LogHandler) LogError(msg string) {
  fmt.Println(fh.prefix, msg);
}

func main() {
  logHandler := &LogHandler{prefix: "debug: "}
  metrics.InitStats("mysvc", 5, 1000, logHandler)

  metrics.RegisterStat("HTTP.GET", Accumulator, nil)
  metrics.RegisterStat("Method.Get", Average, nil)
  metrics.RegisterStat(("under500ms", Percentile, 500)
  metrics.RegisterStat(("Total.errors", Percentile, 0)
  metrics.RegisterStat(("Total.success", Percentile, 0)
  ...
}

func Get() err {
  metrics.UpdateStat("HTTP.GET", 1)
  timer := metrics.StartTimerNanos("Method.Get")
  defer metrics.EndTimer(timer)
  ...
  if (err != nil) {
    metrics.UpdateStat("Total.errors", 1) // 1 > 0 == error
    return err
  }
  
  metrics.UpdateStat("Total.errors", -1) // -1 < 0 == no error
  ...
}
```


