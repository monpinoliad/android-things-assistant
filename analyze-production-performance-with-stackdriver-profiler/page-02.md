## 2. Setup and Requirements
### Self-paced environment setup

If you don't already have a Google Account (Gmail or Google Apps), you must [create one](https://accounts.google.com/SignUp). Sign-in to Google Cloud Platform console ([console.cloud.google.com](http://console.cloud.google.com/)) and create a new project:

![](https://lh6.googleusercontent.com/iHhcE5E3Q9EgC0Co0aG_zRe4EDbsNp7NjBbihV02Hx74kNFxmchTs3SVt8HpTImo0QdYw1llpgC2YLilbHr1Iohyy3xcG3ySL5L9n6Sjfy7iHwtEppHPdu2S7AfiL0faNlOB_Gzl)

Remember the project ID, a unique name across all Google Cloud projects (the name above has already been taken and will not work for you, sorry!). It will be referred to later in this codelab as `PROJECT_ID`.

Next, you'll need to [enable billing](https://console.cloud.google.com/billing) in the Cloud Console in order to use Google Cloud resources.

Running through this codelab shouldn't cost you more than a few dollars, but it could be more if you decide to use more resources or if you leave them running (see "cleanup" section at the end of this document).

New users of Google Cloud Platform are eligible for a [$300 free trial](https://console.developers.google.com/billing/freetrial?hl=en).

### Google Cloud Shell

While Google Cloud can be operated remotely from your laptop, to make the setup simpler in this codelab we will be using [Google Cloud Shell](https://cloud.google.com/cloud-shell/), a command line environment running in the Cloud.
#### Activate Google Cloud Shell

From the GCP Console click the Cloud Shell icon on the top right toolbar:

![](https://lh4.googleusercontent.com/7EBr6OLFS04RwQtl_O1zZ5_RuJb880FSbaEx-YKRj8WqNCpzVDoC1xuQZCxdMLyaJGlufCDDu0_5-Nrwo4C7b8B8pcEQYyirBtzMSbHsoLTPRw9M2aiT8JFxuZl97Ekj_thd5pfb)

Then click "Start Cloud Shell":

![](https://lh6.googleusercontent.com/KvLqPq-kXV2a12bLtR6QPpUgwNOsb8uTariZXL4Gub_rIoO-M6etpuU27Z0oCO37OWV4rxODSeforx4rHsbhmqgnoVc8U34OT5okYMJXEhagidwMxiBZo2pSmqQjdl4oxPkw4BWg)

It should only take a few moments to provision and connect to the environment:

![](https://lh5.googleusercontent.com/o4xbMf1aTN5Yy-lrvmMhiMsaS76DmngbCC6IDr-4B2EBlDOL3wHl5_lrj6zz6ngFJMcXaLBjhjNVBpF_BAullBz-aMLpjMCg3Cqnl2S99JkOZu4o1uLwk9iM1qkXDU-MlEOKgksc)

This virtual machine is loaded with all the development tools you'll need. It offers a persistent 5GB home directory, and runs on the Google Cloud, greatly enhancing network performance and authentication. Much, if not all, of your work in this lab can be done with simply a browser or your Google Chromebook.

Once connected to the cloud shell, you should see that you are already authenticated and that the project is already set to your *PROJECT_ID*.

Run the following command in the cloud shell to confirm that you are authenticated:

```
gcloud auth list
```

##### Command output

```
Credentialed accounts:
 - <myaccount>@<mydomain>.com (active)
```

> **Note:** gcloud is the powerful and unified command-line tool for Google Cloud Platform. Full documentation is available from [https://cloud.google.com/sdk/gcloud](https://cloud.google.com/sdk/gcloud/). It comes pre-installed on Cloud Shell. You will notice its support for tab-completion.

```
gcloud config list project
```

##### Command output

```
[core]
project = <PROJECT_ID>
```

If it is not, you can set it with this command:

```
gcloud config set project <PROJECT_ID>
```

##### Command output

```
Updated property [core/project].
```
