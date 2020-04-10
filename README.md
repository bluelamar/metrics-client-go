# metrics-client-go
A metrics client API that can send metric records to configurable backend target

The client provides API for an application to update statistics during its runtime.
These updates are reported at end of time windows. Whereupon the stats are sent to the target server or file.

The backend targets are configurable and the package comes with builtin targets for directory and Cassandra.
However, the user may specify their own backend target.

There are default common metrics supported via simple API.
1. memory usage: func UpdateMemoryUsage()
2. disk usage: func UpdateDiskUsage()
3. resource usage: func UpdateResourceUsage()

## Types of Metrics Supported

**Accumulator**: accumulates sum of all values added during the time slice
- ex: number times method type called
- ex: method total exec millis

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

  metrics.RegisterStat("GET", Accumulator, nil)
  metrics.RegisterStat(("under500ms", Percentile, 500)
  metrics.RegisterStat(("errors", Percentile, 0)
  metrics.RegisterStat(("success", Percentile, 0)
  ...
}

func Get() err {
  metrics.UpdateStat("GET", 1)
  timer := metrics.StartTimerNanos("Get")
  defer metrics.EndTimer(timer)
  ...
  if (err != nil) {
    metrics.UpdateStat("errors", 1) // 1 > 0 == error
    return err
  }
  
  metrics.UpdateStat("errors", -) // -1 < 0 == no error
  ...
}
```


