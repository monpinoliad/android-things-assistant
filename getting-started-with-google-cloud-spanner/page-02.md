## 2. Setup and Requirements
### Self-paced environment setup

If you don't already have a Google Account (Gmail or Google Apps), you must [create one](https://accounts.google.com/SignUp). Sign-in to Google Cloud Platform console ([console.cloud.google.com](http://console.cloud.google.com/)) and create a new project.

If you already have a project, click on the project selection pull down menu in the upper left of the console:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/edeec676fe75154e.png)

and click the ‘+' icon on the resulting dialog to create a new project:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/6ca3d08c8be8f04a.png)

If you don't already have a project, you should see a dialog like this to create your first one:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/3c50189ec031c0cf.png)

The subsequent project creation dialog allows you to enter the details of your new project:

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/d9806ac44913b12f.png)

Remember the project ID, which is a unique name across all Google Cloud projects (the name above has already been taken and will not work for you, sorry!). It will be referred to later in this codelab as `PROJECT_ID`.

Next, if you haven't already done so,you'll need to [enable billing](https://console.developers.google.com/billing) in the Developers Console in order to use Google Cloud resources and [enable the Cloud Spanner API](https://console.developers.google.com/apis/api/spanner.googleapis.com).

![](https://codelabs.developers.google.com/codelabs/cloud-spanner-first-steps-java/img/bafab6d2e2ea6aa3.png)

Running through this codelab shouldn't cost you more than a few dollars, but it could be more if you decide to use more resources or if you leave them running (see "cleanup" section at the end of this document). Google Cloud Spanner pricing is documented [here](https://cloud.google.com/spanner/pricing).

New users of Google Cloud Platform are eligible for a [$300](https://console.developers.google.com/billing/freetrial?hl=en) free trial, which should make this codelab entirely free of charge.

### Google Cloud Shell Setup

While Google Cloud and Cloud Spanner can be operated remotely from your laptop, in this codelab we will be using [Google Cloud Shell](https://cloud.google.com/cloud-shell/), a command line environment running in the Cloud.

This Debian-based virtual machine is loaded with all the development tools you'll need. It offers a persistent 5GB home directory, and runs on the Google Cloud, greatly enhancing network performance and authentication. This means that all you will need for this codelab is a browser (yes, it works on a Chromebook).

To activate Google Cloud Shell, from the developer console simply click the button on the top right-hand side (it should only take a few moments to provision and connect to the environment):

![](https://lh4.googleusercontent.com/aTWW5eqtihrAnSHQEocSQagOiQ_5WrQ412SqbeUwuTy7gdeDYp9PuExUS1W_ZTJUqXUWTiI9az55Gq3k7_7VPRUpT6v1mQyLj1hKPn-Qhtj6AZDs82NfZOesWucAPzqqTXG4Iq8q)

Then accept the terms of service and click the "Start Cloud Shell" link:

![](https://lh4.googleusercontent.com/JAYYQ2Or-2dVym1jWtAwnF7wrK1GxMsxUKIsLggOTkmtKMx0noCE_UdUN6vizw5MYrQtQuIKQxHoV2wTS2DhLqcNlgCvMzg9-Zc8mRsdU63QR2NnZXb-FaV-fO5JTBrD7T66Jofo)

![](https://lh6.googleusercontent.com/MlVK1apzXKq0v4faXqeuirITzJfezMoFVIghY9_OpVQNYnYollNKtiCLAwnZ2XCeI9qOwqN1k5dsi7zRFgC5EXXOwlSBUo8RWFLBMKN8VJDL544MzIcxv-x6Cm8tAiE0sNzgXpAP)

Once connected to the cloud shell, you should see that you are already authenticated and that the project is already set to your `PROJECT_ID` :

``` gcloud auth list ```

#### Command output

```
Credentialed accounts:
 - <myaccount>@<mydomain>.com (active)
```

> **Note:** gcloud is the powerful and unified command-line tool for Google Cloud Platform. Full documentation is available from [https://cloud.google.com/sdk/gcloud](https://cloud.google.com/sdk/gcloud). It comes pre-installed on CloudShell and you will surely enjoy its support for tab-completion.

``` gcloud config list project ```

Command output

```
[core]
project = <PROJECT_ID>
```

If for some reason the project is not set, simply issue the following command :

```
gcloud config set project <PROJECT_ID>
```

Looking for your `PROJECT_ID`? Check out what ID you used in the setup steps or look it up in the console dashboard:

![](https://lh4.googleusercontent.com/_c-lkHjF_mx4O5NYDoPlScyjQgkAWpynuDznqW8gR7-UseawO-81H49x-ZbtX4d6bU8N9TUDboJT4VRSiBP_Hhxr3YcwuLO8PdlPAvdA0oX7iYgQS1DPkKM-03PTWQpZiDZjjWDU)

IMPORTANT: Finally, set the default zone and project configuration:

``` gcloud config set compute/zone us-central1-f ```

You can choose a variety of different zones. Learn more in the [Regions & Zones documentation](https://cloud.google.com/compute/docs/regions-zones/regions-zones).

> **Note:** When you run gcloud on your own machine, the config settings would've been persisted across sessions. But in Cloud Shell, you will need to set this for every new session or reconnection.
