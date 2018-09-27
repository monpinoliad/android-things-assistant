## 4. Profile the Benchmark

We'll use a simple synthetic Go application [available on Github](https://github.com/GoogleCloudPlatform/golang-samples/tree/master/profiler/hotapp). In the Cloud Shell terminal that you still have open (and while the "No data to display" message still shown in the Profiler UI), run the following command:

```
$ go get -u github.com/GoogleCloudPlatform/golang-samples/profiler/...
```

Then switch to the application directory:

```
$ cd ~/gopath/src/github.com/GoogleCloudPlatform/golang-samples/profiler/hotapp
```

The directory contains the "main.go" file, which is a synthetic app that has the profiling agent enabled:

#### main.go

```
...
import (
        ...
        "cloud.google.com/go/profiler"
)
...
func main() {
        err := profiler.Start(profiler.Config{
                Service:        "hotapp-service",
                DebugLogging:   true,
                MutexProfiling: true,
        })
        if err != nil {
                log.Fatalf("failed to start the profiler: %v", err)
        }
        ...
}
```

The profiling agent collects CPU, heap and thread profiles by default. The code here enables the collection of mutex (also known as "contention") profiles.

Now, run the program:

```
$ go run main.go
```

> **Note:** This codelab runs the program in Cloud Shell purely for illustrative purpose. In real world, you would deploy your program with the profiling agent enabled to a production environment like Kubernetes Engine, App Engine flexible environment or Compute Engine. See [Go profiling docs](https://cloud.google.com/profiler/docs/profiling-go) for more detail.

As the program runs, the profiling agent will periodically collect profiles of the four configured types. The collection is randomized over time (with average rate of one profile per minute for each of the types), so it may take up to three minutes to get each of the types collected. The program tells you when it creates a profile. The messages are enabled by the `DebugLogging` flag in the configuration above; otherwise, the agent runs silently:

```
$ go run main.go
2018/03/28 15:10:24 profiler has started
2018/03/28 15:10:57 successfully created profile THREADS
2018/03/28 15:10:57 start uploading profile
2018/03/28 15:11:19 successfully created profile CONTENTION
2018/03/28 15:11:30 start uploading profile
2018/03/28 15:11:40 successfully created profile CPU
2018/03/28 15:11:51 start uploading profile
2018/03/28 15:11:53 successfully created profile CONTENTION
2018/03/28 15:12:03 start uploading profile
2018/03/28 15:12:04 successfully created profile HEAP
2018/03/28 15:12:04 start uploading profile
2018/03/28 15:12:04 successfully created profile THREADS
2018/03/28 15:12:04 start uploading profile
2018/03/28 15:12:25 successfully created profile HEAP
2018/03/28 15:12:25 start uploading profile
2018/03/28 15:12:37 successfully created profile CPU
...
```

The UI will update itself shortly after first of the profiles is collected. It won't auto-update after that, so to see the new data, you'll need to refresh the Profiler UI manually. To do that, click the Now button in the time interval picker twice:

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/167352e2ffdf1748.png)

After the UI refreshes, you will see something like this:

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/3b0987d69fa03f79.png)

The profile type selector shows the four profile types available:

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/1071de473b7cc102.png)

Let's now review each of the profile types and some important UI capabilities, and then conduct some experiments. At this stage, you no longer need the Cloud Shell terminal, so you can exit it by pressing CTRL-C and typing "exit".