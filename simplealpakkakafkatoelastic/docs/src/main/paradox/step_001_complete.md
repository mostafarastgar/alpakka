## Example code

### Description
- Configure Kafka consumer (1)
- Data class mapped to Elasticsearch (2)
- [Jackson] conversion for the data class (3)
- Elasticsearch client setup (4)
- Kafka consumer with committing support (5)
- Parse message from Kafka to `Movie` and create Elasticsearch write message (6)
- Use `createWithContext` to use an Elasticsearch flow with context-support (so it passes through the Kafka commit offset) (7)
- React on write errors (8)
- Let the `Committer.sink` aggregate commits to batches and commit to Kafka (9)
- Combine consumer control and stream completion into `DrainingControl` (10)

### Data class and JSON mapping

[snip](../../../../src/main/java/com/mostafa/alpakka/simplealpakka/Movie.java)

### Flow

[snip](../../../../src/main/java/com/mostafa/alpakka/simplealpakka/Main.java) { #flow }


### Kafka setup

[snip](../../../../src/main/java/com/mostafa/alpakka/simplealpakka/Main.java) { #kafka-setup }

### Elasticsearch setup

[snip](../../../../src/main/java/com/mostafa/alpakka/simplealpakka/Main.java) { #es-setup }

