# Prompt based approach for knowledge graph construction

<p align="center">
  <img src="https://github.com/user-attachments/assets/c704e166-f9d4-4cfc-aa49-5ec2a2318197" width="200" height="400" />
</p>

## Introduction

In this work, an approach for extracting structured data from PDF’s suing a Retrieval augmented
generation (RAG) approach is discussed. From the extracted information a Knowledge Graph
with meaningful relationships is generated. This method is particularly useful for processing
research papers that contain sensor data and relationships between their parts.

## Methodology

The approach consists of four main steps

- Extracting relevant Lidar sensor data using a retrieval mechanism.
- Processing the retrieved text using a LLM and finding the complex relationships between
  the sensor data.
- Converting the extracted information into a knowledge graph.
- DoaQuality assurance of the extracted sensor data.

## Data Retrieval

FAISS and BM25 retrievals were used for text extraction due to the following factors:

- The retriever provides fast and accurate retrieval of relevant content from the text thus
  minimizing unnecessary data processing.
- It captures semantically relevant text instead of relying solely on string matching.

- How Retrieval Works
- Indexing the PDF content into a vector store using embeddings.
- Using a small prompt to find relevant text snippets.
- Ranking retrieved passages based on relevance scores.
- Passing the most relevant documents to the LLM for further processing and knowledge
  extraction.

The retriever ensures that only the most relevant information is passed to the LLM, reducing
noise and improving the precision of extracted knowledge.

## Ontology Classes

- Device Represents a general device that contains a sensor.
- Sensor Component Represents a sensor category within a device.
- Sensor Represents an individual Lidar sensor.
- Part Represents components of a sensor.
- Property Represents a specific property of a sensor.
- Technology Represents a technology that a sensor implements.

- Object Properties
- has_part_directly (Sensor → Part): Links sensors to their parts.
- has_property (Sensor → Property): Associates Lidar sensors with their properties.
- implements (Sensor → Technology): Represents technology that a Lidar sensor imple
  ments.
- used_in (Sensor → Device): Links a Lidar sensor to the device it is used in.
<p align="center">
   <img src="https://github.com/user-attachments/assets/d2f057e5-1abc-4c6e-b204-b33686a5f811" width="400" height="400" />
</p>

## Validation

The validation process for Lidar sensor extraction data verifies that the sensor names generated
by the LLM matches the information in the original PDF document or not. The following
approaches were used:

- String Matching

  - This approach uses an exact text search matching technique to determine if the extracted sensor
    name exists in the PDF text or not. It is a check that returns a true or false result. However,
    it is sensitive to tiny errors such as typos and formatting inconsistencies.

- Fuzzy Verification

  - A Python library fuzzywuzzy was used to approximate string matching in order to overcome the
    drawbacks of exact string matching. The token set ratio between each extracted PDF text chunk
    and the extracted sensor name is calculated. For every comparison, a similarity score (ranging
    from 0 to 100) is generated. To decide whether or not the sensor name adequately matches
    a section of the text, a threshold is applied. This approach can withstand little variations in
    formatting and language.

- Semantic Verification

  - A pre-trained sentence-transformers model is used in semantic verification to extract the sensor
    name’s contextual meaning. This method involved creating a query by fusing the name of
    the sensor with its category (for example, "LiDAR sensor model Velodyne VLP-32C used in
    automotive lidar"). The cosine similarity between each PDF chunk and the query is calculated
    after they have both been encoded into embeddings. To determine a semantic match measure,
    the average of the top few scores was used rather than depending only on the highest similarity
    value. When the specific phrase differs but the general idea remains the same, this approach
    can be helpful.

- Confidence Score

  - For each sensor, a weighted confidence score was computed by combining the results of the
    following methods: - String match (contributes 40%) - Fuzzy matching (contributes 30%) - Semantic matching (contributes 30%)

## Visualization

Scatter plots were used to display the results:

- Semantic Score vs. Confidence: This graph shows the relationship between overall confidence and semantic similarity. A better contextual fit is typically indicated by higher
  semantic scores.
- Confidence vs. Fuzzy Score: This plot shows how the confidence is affected by approximate
  string matching.
- Semantic Score vs Fuzzy Scores: This aids in understanding how context-sensitive and
  string-based matching techniques relate to one another.
