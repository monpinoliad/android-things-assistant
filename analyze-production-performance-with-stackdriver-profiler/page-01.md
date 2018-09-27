## 1. Overview

While client app and frontend web developers commonly use tools like the [Android Studio CPU Profiler](https://developer.android.com/studio/profile/cpu-profiler.html) or the [profiling tools included in Chrome](https://developers.google.com/web/tools/chrome-devtools/rendering-tools/) to improve their code's performance, equivalent techniques haven't been nearly as accessible or well adopted by those who work on backend services. [Stackdriver Profiler](https://cloud.google.com/profiler/docs/) brings these same capabilities to service developers, regardless of whether their code is running on the Google Cloud Platform or elsewhere.

![](https://codelabs.developers.google.com/codelabs/cloud-stackdriver-profiler/img/2eee5c243cb1791b.png)

The tool gathers CPU usage and memory-allocation information from your production applications. It attributes that information to the application's source code, helping you identify the parts of the application consuming the most resources, and otherwise illuminating the performance characteristics of the code. Low overhead of the collection techniques employed by the tool makes it suitable for continuous use in production environments.

In this codelab, you will learn how to set up Stackdriver Profiler for a Go program and will get familiar with what kind of insights about application performance the tool can present.

### What you'll learn
- How to configure a Go program for profiling with Stackdriver Profiler.
- How to collect, view and analyze the performance data with Stackdriver Profiler.

### What you'll need
- A Google Cloud Platform project
- A browser, such [Chrome](https://www.google.com/chrome/browser/desktop/) or [Firefox](https://www.mozilla.org/firefox/)
- Familiarity with standard Linux text editors such as Vim, EMACs or Nano
