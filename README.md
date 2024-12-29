
# Legal Document Pseudonymization System

Context & Objective
Lawyers adhere to strict confidentiality and privacy standards, which presents significant challenges when adopting AI assistants in legal work. While AI technologies generally require cloud-based processing, on-premise hosting is often impractical due to these constraints. One possible solution is pseudonymizing sensitive information in legal documents before transmitting them to cloud services.

The objective of this system is to prototype an approach that automatically detects and pseudonymizes sensitive entities within legal documents—such as names of individuals and private organizations—while leaving references to public authorities intact. This is a crucial step in ensuring that sensitive legal information is protected when utilizing AI-powered tools.

# Approach
1. LLM for Named Entity Recognition (NER) and Postprocessing
In this project, we use a Large Language Model (LLM) for Named Entity Recognition (NER), which enables us to automatically detect and classify sensitive entities in legal documents, such as the names of individuals, private organizations, and public authorities. Given that legal documents can be written in various languages, LLMs are ideal because they are language-agnostic and can handle a wide variety of languages without the need for retraining on specific linguistic datasets.

###### Postprocessing:
Once the NER task is completed, postprocessing is performed to refine the output:

Span extraction: The postprocessing function extracts the spans (the exact locations within the text) of the detected entities, allowing the system to map back to the original text. This ensures that the system knows where each entity occurs within the document.

Pseudonymization: After extracting the spans, the sensitive entities (such as names of individuals and organizations) are pseudonymized. Pseudonymization involves replacing these entities with anonymous identifiers or pseudonyms to protect privacy while maintaining the integrity of the legal text. The system ensures that public authorities (e.g., court names, governmental bodies) are not pseudonymized, preserving the document’s reference to these entities.

The postprocessing steps ensure that the text remains usable for further legal analysis while protecting sensitive data.

###### Model Selection:
One-shot prompt using the Mistral-7B-Instruct-v0.1.
Fine-tuned Mistral model with additional training specific to legal NER tasks.
You can choose between these two modes by specifying the few_shot_mode=True parameter when instantiating the model.

###### Why Choose LLM?
Since the input language is not known beforehand, LLMs are preferred because they are language-agnostic and capable of performing well in a wide variety of linguistic contexts. Training a dedicated model for one language would result in poor performance when applied to other languages, making LLM the best option for this project.

### 2. How to Use the System
To use this system, there is a class named Legal_Document_NER that you can instantiate, specifying the mode you want to use (either one_shot or finetuned):


##### Instantiate the class with the mode of your choice
``` python
legal_ner_system = Legal_Document_NER(few_shot_mode=True)  # or False for using fine-tuned model```

##### Extract NER, spans, and pseudonymized text
```python
name_entities, spans, pseudonymized_text  = legal_ner_system(text)```
The class automatically handles the text processing for you, extracting named entities, identifying spans, and pseudonymizing sensitive information while preserving public authority references.

#### 3. Postprocessing
After detecting the entities, a postprocessing function is used to further refine the pseudonymization process. This step ensures that the entities are replaced with pseudonyms while maintaining the context intact.



### Installation
You can install the necessary dependencies by running:

```pip install -r requirements.txt```
### Usage

Open the Notebook, install the dependencies, instantiate the Legal_Document_NER class with the prefered mode, and enter the text for which NER is applied.

### Instantiate the system with your preferred mode (one-shot or fine-tuned)
```python
legal_ner_system = Legal_Document_NER(few_shot_mode=True)

### Pass your legal text to the system
text = "Insert your legal document text here"
name_entities, spans, pseudonymized_text = legal_ner_system(text)

print(name_entities)            #-> This is a dictionary of format : {NER_category: [list of name_entities] ...}
print(spans)                    #-> This is a Json object of format : [ {'start': **, 'end':**, 'label':**, 'text':**} ...]
print(pseudonymized_text)       #-> This is a string object of the pseudonymzed text with ordered name_entity category. 

```


# Development Plan
#### 1. Improving the System
Expand the dataset: Currently, only 20% of the dataset has been used for fine-tuning the Mistral model. Increasing the dataset size will likely improve the system's performance and accuracy.
Training a dedicated NER model: In addition to the LLM-based solution, we could train a non-LLM-based model specifically designed for NER. This model would perform better but would be limited to the language it was trained on. However, this approach would require additional resources and may lack the multilingual flexibility that the LLM offers.
#### 2. Hybrid Approach
The most optimal approach could be a hybrid model that combines the strengths of both a dedicated NER model and a fine-tuned LLM. This would ensure more accurate and context-aware pseudonymization for the specific language(s) in the documents while maintaining the flexibility of LLM for multilingual support.

#### 3. Finetuning BERT for NER
Finetuning BERT for NER could improve the system's performance, particularly because BERT is a bidirectional model. This allows it to capture context from both sides of a token, which can lead to more accurate entity detection, especially in complex legal texts where context is crucial for distinguishing between entities.

# Time

The task requires some time to complete. Fine-tuning is timeconsuming given the computational cost. I started finetuning saturday's night and up to now (sunday, afternoon) the finetunins is still undone (taking in consideration that the sytem crushes before finishin the epoch), though QLora finetuning is adopted.
