## 1. Introduction

Cloud IoT Core is a fully managed service that allows you to easily and securely connect, manage, and ingest data from millions of globally dispersed devices. Cloud IoT Core, in combination with other services on Google Cloud platform, provides a complete solution for collecting, processing, analyzing, and visualizing IoT data in real time to support improved operational efficiency.

![](https://codelabs.developers.google.com/codelabs/cloud-iot-core-overview/img/99e2ef696308e843.png)

This codelab gives you a hands-on walkthrough to setting up and configuring IoT devices using Cloud IoT Core. You will transmit telemetry messages from a device and the device will respond to configuration changes from a server based on real-time data.

> Looking for more? Check out the [Building for Enterprise](https://www.youtube.com/watch?v=ETWhOWvqH5E) and [IoT and Cloud for Industrial Applications](https://www.youtube.com/watch?v=ZFu5gpGEYj0) from Google I/O 2017. You can also [listen](https://www.gcppodcast.com/post/episode-101-iot-core/) to the Google Cloud Platform Podcast episode on Cloud IoT Core. You can also [learn how to improve IoT security](https://www.youtube.com/watch?v=A32RFbkozFc) by securing the authentication between your device and Google Cloud IoT.

### What you'll build

In this codelab, you'll build a simple but complete IoT system. The devices in this system publish temperature data to their telemetry feeds, and a server consumes the telemetry data from a Cloud Pub/Sub topic. The server then decides whether to turn on or off the individual devices' fans, via a Cloud IoT Core configuration update.

![](https://codelabs.developers.google.com/codelabs/cloud-iot-core-overview/img/e7232d5c3c53d8f2.png)

What you'll learn
- How to create a device registry and add a device to it
- How to provision a device and transmit telemetry data from it
- How to control a device using a server based on a telemetry stream
- [Optional] How to go from virtual device to hardware

### What you'll need

- Access to [Google Cloud Shell](https://cloud.google.com/shell/docs/), available in [Google Cloud Console](https://console.cloud.google.com/home/dashboard)

This codelab focuses on Google Cloud IoT Core. Tangential concepts and code blocks are glossed over and are provided for you to simply copy and paste.
