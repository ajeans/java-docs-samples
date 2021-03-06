# Cloud Data Loss Prevention (DLP) API Samples

<a href="https://console.cloud.google.com/cloudshell/open?git_repo=https://github.com/GoogleCloudPlatform/java-docs-samples&page=editor&open_in_editor=dlp/README.md">
<img alt="Open in Cloud Shell" src ="http://gstatic.com/cloudssh/images/open-btn.png"></a>

The [Data Loss Prevention API](https://cloud.google.com/dlp/docs/) provides programmatic access to 
a powerful detection engine for personally identifiable information and other privacy-sensitive data
 in unstructured data streams.

## Setup
- A Google Cloud project with billing enabled
- [Enable](https://console.cloud.google.com/launcher/details/google/dlp.googleapis.com) the DLP API.
- (Local testing) [Create a service account](https://cloud.google.com/docs/authentication/getting-started)
and set the `GOOGLE_APPLICATION_CREDENTIALS` environment variable pointing to the downloaded credentials file.
- (Local testing) Set the `DLP_DEID_WRAPPED_KEY` environment variable to an AES-256 key encrypted ('wrapped') [with a Cloud Key Management Service (KMS) key](https://cloud.google.com/kms/docs/encrypt-decrypt).
- (Local testing) Set the `DLP_DEID_KEY_NAME` environment variable to the path-name of the Cloud KMS key you wrapped `DLP_DEID_WRAPPED_KEY` with.

## Build
This project uses the [Assembly Plugin](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html) to build an uber jar.
Run:
```
   mvn clean package -DskipTests
```

## Retrieve InfoTypes
An [InfoType identifier](https://cloud.google.com/dlp/docs/infotypes-categories) represents an element of sensitive data.

[InfoTypes](https://cloud.google.com/dlp/docs/infotypes-reference#global) are updated periodically. Use the API to retrieve the most current InfoTypes.
  ```
    java -cp target/dlp-samples-1.0-jar-with-dependencies.jar dlp.snippets.InfoTypesList
  ``` 

## Run the quickstart

The Quickstart demonstrates using the DLP API to identify an InfoType in a given string.

Note that you will need to set the `projectId` in
[QuickStart.java](https://github.com/GoogleCloudPlatform/java-docs-samples/blob/master/dlp/src/main/java/dlp/snippets/QuickStart.java)
for this to work correctly. If you forget this, you will see a `PERMISSION_DENIED` error.

```
   java -cp target/dlp-samples-1.0-jar-with-dependencies.jar dlp.snippets.QuickStart
```

## Inspect data for sensitive elements
Inspect strings, files locally and on Google Cloud Storage, Cloud Datastore, and BigQuery with the DLP API.

Note: image scanning is not currently supported on Google Cloud Storage.
For more information, refer to the [API documentation](https://cloud.google.com/dlp/docs). 
Optional flags are explained in [this resource](https://cloud.google.com/dlp/docs/reference/rest/v2beta1/content/inspect#InspectConfig).

## Automatic redaction of sensitive data from images
[Automatic redaction](https://cloud.google.com/dlp/docs/redacting-sensitive-data-images) produces an output image with sensitive data matches removed.

### Example
- Redact phone numbers and email addresses from `src/test/resources/test.png`:

  Note that you will need to set the `projectId` in
  [RedactImageFile.java](https://github.com/GoogleCloudPlatform/java-docs-samples/blob/master/dlp/src/main/java/dlp/snippets/RedactImageFilet.java)
  for this to work correctly. If you forget this, you will see a `PERMISSION_DENIED` error.

  ```
    java -cp target/dlp-samples-1.0-jar-with-dependencies.jar dlp.snippets.RedactImageFile
  ```

## Integration tests
### Setup
- Ensure that `GOOGLE_APPLICATION_CREDENTIALS` points to authorized service account credentials file.
- [Create a Google Cloud Storage bucket](https://console.cloud.google.com/storage) and upload [test.txt](src/test/resources/test.txt).
    - Set the `GCS_PATH` environment variable to point to the path for the bucket.
- Copy and paste the data below into a CSV file and [create a BigQuery table](https://cloud.google.com/bigquery/docs/loading-data-local) from the file:
    ```$xslt
    Name,TelephoneNumber,Mystery,Age,Gender
    James,(567) 890-1234,8291 3627 8250 1234,19,Male
    Gandalf,(123) 456-7890,4231 5555 6781 9876,27,Male
    Dumbledore,(313) 337-1337,6291 8765 1095 7629,27,Male
    Joe,(452) 123-1234,3782 2288 1166 3030,35,Male
    Marie,(452) 123-1234,8291 3627 8250 1234,35,Female
    Carrie,(567) 890-1234,2253 5218 4251 4526,35,Female
    
    ```
  - Set the `BIGQUERY_DATASET` and `BIGQUERY_TABLE` environment values.
- [Create a Google Cloud Pub/Sub](https://console.cloud.google.com/datastore) topic and and a subscription that is subscribed to the topic.
    - Set the `PUB_SUB_TOPIC` and `PUB_SUB_SUBSCRIPTION` environment variables to the corresponding values.
- [Create a Google Cloud Datastore](https://console.cloud.google.com/datastore) kind and add an entity with properties:
  - `property1` : john@doe.com
  - `property2` : 343-343-3435
-  Update the Datastore kind in [InspectTests.java](src/test/java/dlp/snippets/InspectTests.java).


## Run
Run all tests:
  ```
     mvn clean verify
  ```
