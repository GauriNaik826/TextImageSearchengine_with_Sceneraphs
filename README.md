# Text-to-Image Search Engine with Scene Graphs

## Abstract
Recent advancements in Vision-Language Models (VLMs) have significantly improved image retrieval systems, enabling more nuanced matching between textual queries and visual content. However, current models still struggle with accurately interpreting and retrieving images based on complex spatial relationships and object interactions described in multi-part queries. 

We focus on overcoming the tendency of existing models to behave as "bags-of-words," which often results in misinterpretation of spatial and contextual relationships in complex queries. This limitation hinders the effectiveness of image search engines in scenarios requiring precise compositional understanding, such as e-commerce, healthcare, and education. 

We propose a novel framework that fuses scene graph embeddings, derived from Graph Neural Networks (GNNs), with text and image embeddings from VLMs. This fusion improves the relational information of the embeddings, thereby enhancing the model's ability to retrieve images that accurately match complex, multi-part queries. Our approach shows a significant improvement of 4–5% in retrieval accuracy.

![Methodology Diagram](https://github.com/user-attachments/assets/8fd177a3-13b2-42f3-a998-a2e4ff432b65)
<p align="left">
Methodology for text-to-image retrieval using Scene Graph embeddings. The system combines image embeddings from a CLIP Image Encoder with Scene Graph embeddings generated from the image dataset to produce fused image embeddings, which are indexed using Annoy trees for efficient similarity search. For the example text query, "person with dark pants sitting beside a clear window," the CLIP Text Encoder generates embeddings that are fused with Scene Graph embeddings from the query. This enables approximate nearest neighbor search, retrieving the top-ranked images, such as a person sitting by a window, aligning closely with the query description.
</p>

---

## Methodology

This section outlines the methodology used to improve the compositional understanding of Vision-Language Models (VLMs) by incorporating relational information into text and image embeddings of VLMs using scene graphs. By addressing complex object relationships and contextual dependencies, we improve the accuracy and relevance of text-to-image retrieval systems.

As illustrated in the figure above, the proposed system integrates a Vision-Language Model (VLM) with scene graph embeddings. The methodology comprises three main steps, as described below.

### 1. Creating the Annoy Index with Image Embeddings
The first step in the retrieval task involves building a structured storage system for image embeddings using the Annoy index. First, the images from the dataset are passed through the CLIP image encoder, which generates embeddings representing the visual content of each image. Simultaneously, the corresponding scene graphs for these images—where nodes represent objects and edges depict the relationships between them—are processed through a fine-tuned Graph Neural Network (GNN). The GNN is fine-tuned on the FACTUAL scene graph dataset \cite{li-etal-2023-factual}. The GNN encodes the scene graphs into embeddings that capture the relational structures within the images. These scene graph embeddings are then fused with the CLIP image embeddings, resulting in enriched feature vectors that incorporate both spatial and relational understanding. These fused embeddings are stored in the Annoy index \footnote{https://github.com/spotify/annoy}, which organizes embeddings into a tree structure using random projections, facilitating efficient clustering and retrieval of similar embeddings. Once this indexed database of image embeddings is prepared, the system is ready to process textual queries.


### 2. Query Representation
As shown in the figure, when a text query is provided, it is processed in parallel with its corresponding scene graph. The raw textual query is passed through the CLIP text encoder to generate its feature embedding that captures the semantic meaning of the query. In addition, a scene graph is constructed for the query, where objects (e.g., ``person'', ``pants'', ``window'') serve as nodes and their relationships (e.g., ``person-with-pants'') define the edges. This query scene graph is encoded using the same fine-tuned GNN used for the image scene graphs, producing embeddings that capture the relational structure of the query. The text embeddings from the CLIP text encoder are fused with the query scene graph embeddings, resulting in a unified query representation that incorporates both semantic and relational understanding to better interpret complex, multi-part queries.

### 3. Retrieval Using Approximate Nearest Neighbors
The final step involves retrieving the most relevant images from the indexed database. The fused query embeddings are compared with the fused image embeddings stored in the Annoy index. The index leverages the Approximate Nearest Neighbors (ANN) algorithm to perform a similarity search, clustering the stored embeddings and identifying the closest matches to the query embedding in the shared vector space. This process efficiently retrieves the top-ranked images that are most relevant to the query in terms of both semantic content and relational context. By integrating scene graph-based reasoning and fusing the embeddings, our system achieves improved compositional understanding, enabling it to handle complex multi-part queries with higher accuracy and relevance.


