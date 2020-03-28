# Alpakka sample

## Fetch CSV via Akka HTTP and publish the data as JSON to Kafka

This example uses @extref[Akka HTTP to send the HTTP request](akka-http:client-side/connection-level.html#opening-http-connections) and Akka HTTPs primary JSON support via Jackson JSON (for Java) to convert the map into a JSON structure which gets published to a Kafka topic.

Browse the sources at @link:[Github](https://github.com/akka/alpakka-samples/tree/master/alpakka-sample-http-csv-to-kafka) { open=new }.

To try out this project clone @link:[the Alpakka Samples repository](https://github.com/akka/alpakka-samples) { open=new } and find it in the `alpakka-sample-http-csv-to-kafka` directory.

## Step 1: Send HTTP request

### Description

This code uses Akka HTTP to request a file containing listed companies from the NASDAQ web site.

It starts the Actor System, imports the Actor System's dispatcher as `ExecutionContext`, and gets a stream materializer from the Actor System.

The HTTP request is created as value (it will be sent multiple times in later steps) and sets a specific HTTP request header.

The request is run in an Akka Stream from the single value, issuing the request by Akka HTTP, and printing out the HTTP response.

Once the stream completes, the Actor System is terminated and the program exits.

## Step 2: extract HTTP entity

### Description

The HTTP response with status OK is expected and the contained HTTP entity is extracted. Instead of the HTTP response, the contained entity (page content) continues in the stream in the form of [ByteString](akka.util.ByteString) elements.

## Step 3: parse CSV

### Description

The binary data in [ByteString](akka.util.ByteString)s is passed into @extref:[Alpakka CSV](alpakka:data-transformations/csv.html) to be parsed and converted per line into a Map. The stream elements becomes a @java[`Map<String, ByteString>`], one entry per column using the column headers as keys.

# Step 4: Producing JSON

### Description

The helper method `toJson` turns the maps into JSON by using @java[Jackson].

# Step 5: Cleanse lines from data source

### Description

The CSV data we receive happens to contain an empty column without a header. The `cleanseCsvData` removes that column and turns the column values from [ByteString](akka.util.ByteString)s into regular `String`s.

# Step 6: Adding Coordinated Shutdown

### Description

To make sure Akka HTTP is shut down in a proper way it is added to Akka's @extref:[Coordinated shutdown](akka:actors.html#coordinated-shutdown).

# Step 7: Produce to Kafka

### Description

[Testcontainers](https://www.testcontainers.org/) starts a Kafka broker in Docker. 

@extref:[Alpakka Kafka](alpakka-kafka:producer.html) producer settings specify the broker address and the data types for Kafka's key and value.

Producer.plainSink sends the `ProducerRecord`s stream elements to the specified Kafka topic.

# Step 8: Scheduled download

### Description

Use `Source.tick` to run the HTTP request every 30 seconds. 

Run the process for about a minute and stop the source after that.
