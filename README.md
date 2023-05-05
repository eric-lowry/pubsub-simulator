# pubsub-simulator
PubSub Simulator

### Create PubSub topics and a default subscription 

```bash
export TOPIC=qwiklab-test
gcloud pubsub topics create $TOPIC
gcloud pubsub topics create $TOPIC-errors
gcloud pubsub subscriptions $TOPIC-sub --topic=$TOPIC
```

### Create the dataflow job

```bash
export CLOUD_ID="...my-elastic-cloud-id..."
export API_KEY="...my-elastic-api-key..."
export GOOGLE_CLOUD_PROJECT=elastic-sa
gcloud dataflow flex-template run qwiklabs-iiot-stream-`date +%s` --template-file-gcs-location gs://dataflow-templates-us-central1/latest/flex/PubSub_to_Elasticsearch --region us-central1 --num-workers 1 --parameters inputSubscription=projects/$GOOGLE_CLOUD_PROJECT/subscriptions/$TOPIC-sub,errorOutputTopic=projects/$GOOGLE_CLOUD_PROJECT/topics/$TOPIC-errors,namespace=iiot,maxNumWorkers=1,apiKey=$API_KEY,connectionUrl=$CLOUD_ID
```

results in a job like this:

```
job:
  createTime: '2023-04-26T18:39:16.131598Z'
  currentStateTime: '1970-01-01T00:00:00Z'
  id: 2023-04-26_11_39_15-8647607649130518914
  location: us-central1
  name: qwiklabs-iiot-stream-1682534354
  projectId: elastic-sa
  startTime: '2023-04-26T18:39:16.131598Z'
```

### Manualy publish some test data

```bash
gcloud pubsub topics publish $TOPIC --message '{"device":"chill-01", "type":"chiller", "temp":30.3}'
gcloud pubsub topics publish $TOPIC --message '{"device":"chill-02", "type":"chiller", "temp":29.8}'
gcloud pubsub topics publish $TOPIC --message '{"device":"freeze-01", "type":"freezer", "temp":2.2}'
```

### Run the simulator

```bash
gcloud auth application-default login
./bin/simulator
```