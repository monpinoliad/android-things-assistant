## 5. Analyze the Profiler Data

Now that we've collected some data, let's look at it more closely. We are using a synthetic app (the source is [available on Github](https://github.com/GoogleCloudPlatform/golang-samples/blob/master/profiler/hotapp/main.go)) that simulates behaviors typical of different kinds of performance issues in production.

> **Note:** This lab only briefly goes over various capabilities of the Profiler user interface. Refer to the [documentation](https://cloud.google.com/profiler/docs/using-profiler) for full information.

### CPU-intensive Code

Select the CPU profile type. After the UI loads it, you'll see in the flame graph the four leaf blocks for `load` function, which collectively account for all the CPU consumption:

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/dd9056849c1dd223.png)

This function is specifically written to consume a lot of CPU cycles by running a tight loop:

#### main.go

```
func load() {
        for i := 0; i < (1 << 20); i++ {
        }
}
```

The function is called indirectly from `busyloop`() via four call paths: `busyloop` → {`foo1`, `foo2`} → {`bar`, `baz`} → `load`. The width of a function box represents the relative cost of the specific call path. In this case all four paths have about the same cost. In a real program, you want to focus on optimizing call paths that matter the most in terms of performance. The flame graph, which visually emphasizes the more expensive paths with larger boxes, makes these paths easy to identify.

You can use the profile data filter to further refine the display. For example, try adding a "Show stacks" filter specifying "baz" as the filter string. You should see something like the screenshot below, where only two of the four call paths to `load()` are displayed. These two paths are the only ones that go through a function with the string "baz" in its name. Such filtering is useful when you want to focus on a subpart of a bigger program (for example, because you only own part of it).

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/7b8a0b3e9ec4598c.png)

### Memory-intensive Code

Now switch to "Heap" profile type. Make sure to remove any filters you created in previous experiments. You should now see a flame graph where `allocImpl`, called by `alloc`, is displayed as the main consumer of memory in the app:

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/5b75c0a793281455.png)

The summary table above the flame graph indicates that the total amount of used memory in the app is on average ~57.4 MiB, most of it is allocated by `allocImpl` function. This is not surprising, given the implementation of this function:

#### main.go

```
func allocImpl() {
        // Allocate 64 MiB in 64 KiB chunks
        for i := 0; i < 64*16; i++ {
                mem = append(mem, make([]byte, 64*1024))
        }
}
```

The function executes once, allocating 64 MiB in smaller chunks, then storing pointers to those chunks in a global variable to protect them from being garbage collected. Note that the amount of memory shown as used by profiler is slightly different from 64 MiB: the Go heap profiler is a statistical tool, so the measurements are low-overhead but not byte-accurate. Don't be surprised when seeing a ~10% difference like this.

### IO-intensive Code

If you choose "Threads" in the profile type selector, the display will switch to a flame graph where most of the width is taken by `wait` and `waitImpl` functions:

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/e2146411028e7341.png)

In the summary above flame graph, you can see that there are 100 goroutines that grow their call stack from the `wait` function. This is exactly right, given that the code that initiates these waits looks like this:

#### main.go

```
func main() {
        ...
        // Simulate some waiting goroutines.
        for i := 0; i < 100; i++ {
                go wait()
        }
```

This profile type is useful for understanding whether the program spends any unexpected time in waits (like I/O). Such call stacks would not be typically sampled by the CPU profiler, as they don't consume any significant portion of the CPU time. You'll often want to use "Hide stacks" filters with Threads profiles - for example, to hide all stacks ending with a call to `gopark`, since those are often idle goroutines and less interesting than ones that wait on I/O.

The threads profile type can also help identify points in program where threads are waiting for a mutex owned by another part of the program for a long period, but the following profile type is more useful for that.

### Contention-intensive Code

The Contention profile type identifies the most "wanted" locks in the program. This profile type is available for Go programs but must be explicitly enabled by specifying "`MutexProfiling: true`" in the agent configuration code. The collection works by recording (under the "Contentions" metric) the number of times when a specific lock, when being unlocked by a goroutine A, had another goroutine B waiting for the lock to be unlocked. It also records (under the "Delay" metric) the time the blocked goroutine waited for the lock. In this example, there is a single contention stack and the total wait time for the lock was 11.03 seconds:

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/41944d5cdf7dfe5b.png)

The code that generates this profile consists of 4 goroutines fighting over a mutex:

#### main.go

```
func contention(d time.Duration) {
        contentionImpl(d)
}

func contentionImpl(d time.Duration) {
        for {
                mu.Lock()
                time.Sleep(d)
                mu.Unlock()
        }
}
...
func main() {
        ...
        for i := 0; i < 4; i++ {
                go contention(time.Duration(i) * 50 * time.Millisecond)
        }
}
```
