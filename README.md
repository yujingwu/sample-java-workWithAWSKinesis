# Build a sample system to play with the intergration between ARTIK Cloud and Kinesis

**Do not peek!!! Work in progress.**

TODO

After completing this sample, you will learn the following objectives:

- TODO
- TODO.

## Requirements
- Java version >= 1.8
- Apache Maven 3.0.5 or above
- [AWS SDK for Java](https://aws.amazon.com/developers/getting-started/java/)
- [Amazon Kinesis Streams setup](http://docs.aws.amazon.com/streams/latest/dev/before-you-begin.html)

## Setup / Installation

### Setup at ARTIK Cloud???

### Setup two Java projects

 1. Clone this sample application if you haven't already done so.
 2. CD to `sub-kinesis-stream-to-akc` directory and run the following build command:
  ~~~shell
  mvn clean package
  ~~~
  The executable `target/sub-kinesis-to-akc-x.x.jar` is generated under the target directory. Type the following to learn the usage:
  ~~~shell
  java -jar sub-kinesis-to-akc-x.x.jar -h
  ~~~

  3.  Cd to `kinesis-stream-consumer` directory of the source root and run the following build command:
  ~~~shell
  mvn clean package
  ~~~
  The executable `target/read-stream-x.x.jar` is generated under the target directory. Type the following to learn the usage:
  ~~~shell
  java -jar read-stream-x.x.jar -h
  ~~~

## Send data to ARTIK Cloud and view via Kinesis stream
Are you ready to have fun?

 1. Create a Kinesis stream with *one* shard on aws site. For example, you can do it using the [AWS Kinesis Console](http://docs.aws.amazon.com/streams/latest/dev/managing-streams-console.html).
 2. Start subscription app to subscribe your Kinesis stream to ARTIK Cloud. Open a terminal and run the following command at the directory where the JAR file is located:
  ~~~shell
  java -jar sub-kinesis-to-akc-x.x.jar -u ARITKCLOUD_UID -t ARTIKCLOUD_TOKEN -k AWS_KEY -s AWS_SECRETE -r KINESIS_STREAM_REGION -n KINESIS_STREAM_NAME
  ~~~
  From the output, you should see the subscription succeeds with a response similar to the following
  ~~~shell
  response code : 200
  response : {"data":{"id":"230117430e6e47","aid":"b6951bf387b84f63","messageType":"message","uid":"240","description":"This is a subscription to user devices","subscriptionType":"awsKinesis","awsKey":"ACRWQ","awsRegion":"us-west-1","awsKinesisStreamName":"akcstream","status":"ACTIVE","createdOn":1489701092932,"modifiedOn":1489701092932}}
  ~~~
  The "id" in the response json is the subscription ID. You will need this ID when deleting this subscription.  
 3. Start device simulator
  ~~~shell
  $ rs 4debdd60ea9f4b1499c7c292b81f001f firesensorGS
  $ Using this token to send the messages: e53342c2f4b245c29b09665f32e4856e
  Send #0 {"temp":3300.747802734375,"onFire":true}
  Got MID: 51f1e30f37f6401f9ac71f24a67eb08f
  Send #1 {"temp":6352.900390625,"onFire":true}
  Got MID: 70db081db5164aaf9bc4a4db4973f820
  Send #2 {"temp":7064.10546875,"onFire":false}
  Got MID: 5f5c8a68718642e490d641b734012b87
  Send #3 {"temp":9339.251953125,"onFire":false}
  Got MID: f2c74e11d50e49d980a72a2cfe2cf4ea
  Send #4 {"temp":4204.416015625,"onFire":false}
  ~~~
 
 4. Start Kinesis stream read app. Open the second terminal and run the following command at the directory where the JAR file is located:
  ~~~shell
  java -jar read-stream-x.x.jar -k AWS_KEY -s AWS_SECRETE -r KINESIS_STREAM_REGION -n KINESIS_STREAM_NAME
  ~~~
  From the terminal print out, you should see that the stream shard iterator has been initialized and the app is now waiting to receive messages from the stream.
  ~~~shell
  Kinesis record: {SequenceNumber: 49571400525989845714667785659406058229971415104434995202,ApproximateArrivalTimestamp: Thu Mar 16 15:16:58 PDT 2017,Data: java.nio.HeapByteBuffer[pos=0 lim=264 cap=264],PartitionKey: 51f1e30f37f6401f9ac71f24a67eb08f}
  ARTIK Cloud message: {"uid":"240bc","cts":1489702616991,"data":{"temp":3300.747802734375,"onFire":true},"mid":"51f1e30f37f6401f9ac71f24a67eb08f","mv":1,"sdid":"4deb","sdtid":"dtce45703593274ba0b4feedb83bc152d8","ts":1489702616991}
  Received 9 records. sleep for 2s ...
  Kinesis record: {SequenceNumber: 49571400525989845714667785659407267155791029802329178114,ApproximateArrivalTimestamp: Thu Mar 16 15:16:59 PDT 2017,Data: java.nio.HeapByteBuffer[pos=0 lim=261 cap=261],PartitionKey: 70db081db5164aaf9bc4a4db4973f820}
  ARTIK Cloud message: {"uid":"240b","cts":1489702618409,"data":{"temp":6352.900390625,"onFire":true},"mid":"70db081db5164aaf9bc4a4db4973f820","mv":1,"sdid":"4debdd60ea9f4b1499c7c292b81f001f","sdtid":"dtce45703593274ba0b4feedb83bc152d8","ts":1489702618409}
  Kinesis record: {SequenceNumber: 49571400525989845714667785659408476081610644500223361026,ApproximateArrivalTimestamp: Thu Mar 16 15:17:00 PDT 2017,Data: java.nio.HeapByteBuffer[pos=0 lim=261 cap=261],PartitionKey: 5f5c8a68718642e490d641b734012b87}
  ARTIK Cloud message: {"uid":"240b","cts":1489702619827,"data":{"temp":7064.10546875,"onFire":false},"mid":"5f5c8a68718642e490d641b734012b87","mv":1,"sdid":"4deb","sdtid":"dtce45703593274ba0b4feedb83bc152d8","ts":1489702619827}
  ~~~
  
 5. After you are done, you can delete the subscription by running the following command:
  ~~~shell
  java -jar sub-kinesis-to-akc-x.x.jar -del ARTIKCLOUD_SUBSCRIPTION_ID  -t ARTIKCLOUD_TOKEN
  ~~~
  You get your subscription ID in the step 2.