# Bridging Information Gaps in Dialogues With Grounded Exchanges Using Knowledge Graphs

## Overview

This repository contains the dialogue corpus, source code, and evaluation output for the study "Bridging Information Gaps in Dialogues With Grounded Exchanges Using Knowledge Graphs" accepted at SIGDIAL 2024.

---

## Citation
> Phillip Schneider, Nektarios Machner, Kristiina Jokinen, and Florian Matthes. 2024. [Bridging Information Gaps in Dialogues with Grounded Exchanges Using Knowledge Graphs](https://aclanthology.org/2024.sigdial-1.10/). In Proceedings of the 25th Annual Meeting of the Special Interest Group on Discourse and Dialogue, pages 110–120, Kyoto, Japan. Association for Computational Linguistics.

**If you use this dataset in your research, please cite the above paper.**

---

## Contents

```
Bridge-KG
├── cg-kg-llm-prediction.ipynb     # Experimental procedures
├── dialogue_corpus/               # Annotated BridgeKG dialogue corpus 
├── domain_datasets/              # Tabular datasets serving as knowledge base 
├── clsruns/                      # Grounding act classification outputs
├── gkruns/                       # Grounded knowledge identification outputs
├── evaluation/                   # Evaluation metrics and analysis
```

---
## Usage Instructions
- Clone the repository and extract all dataset archives
- Launch the Jupyter notebook (`cg-kg-llm-prediction.ipynb`), it contains the experimental procedures including model configuration, prompts, as well as the analysis methods
- Follow the instructions in the notebook to reproduce the experiments:
   - Configure experimental parameters
   - Run grounding act classification and knowledge identification tasks

---
## Examples from the BridgeKG Dataset

### Dialogue Act and Grounded Knowledge Annotations (see Table 4)

| Dialogue Utterances | Dialogue Act | Grounded Knowledge |
|---------------------|--------------|---------------------|
| S: What is your dataset about? | - | - |
| P: It contains information about 11,341 historical figures, including their full name, sex, birth year, city, country, continent, occupation, historical popularity index (HPI). The HPI represents the degree of this person's online popularity. | - | - |
| S: Who is the most popular? | implicit | [{"@context": ["http://www.w3.org/ns/csvw", {"schema": "http://schema.org"}], "@id": "http://example.org/historical-figures", "url": "historical-figures.csv", `"schema:description": "The table contains information about historical figures", "tableSchema": {"columns": [{"name": "full_name", "datatype": "string"}, {"name": "sex", "datatype": "string"}, {"name": "birth_year", "datatype": "integer"}, {"name": "city", "datatype": "string"}, {"name": "country", "datatype": "string"}, {"name": "continent", "datatype": "string"}, {"name": "occupation", "datatype": "string"}, {"name": "historical_popularity_index", "datatype": "float"}], "primaryKey": "full_name"}}`] |
| P: Aristotle, who is from Greece and has the highest HPI value: 31.9938. | - | - |
| S: I see, is there Socrate in the dataset? | explicit | [{"@context": ["http://www.w3.org/ns/csvw", {"schema": "http://schema.org"}], "@id": "http://example.org/historical-figures", "url": "historical-figures.csv", "schema:description": "The table contains information about historical figures", "tableSchema": {"columns": [{"name": "full_name", "datatype": "string"}, {"name": "sex", "datatype": "string"}, {"name": "birth_year", "datatype": "integer"}, {"name": "city", "datatype": "string"}, {"name": "country", "datatype": "string"}, {"name": "continent", "datatype": "string"}, {"name": "occupation", "datatype": "string"}, {"name": "historical_popularity_index", "datatype": "float", `"maximum": 31.9938}]}, "primaryKey": "full_name"}, {"@type": "schema:Person", "full_name": "Aristotle", "country": "Greece", "historical_popularity_index": 31.9938}`] |
|...|...|...|

### Example Prediction Issues (see Table 7)

| Issue Type | Definition | Example |
|------------|------------|---------|
| Invalid JSON-LD | Prediction is overall not a valid JSON-LD structure. | PRED: [{"@type": "schema:CreativeWork", "category": ["films", "series", "literature"]`}]` → `JSON syntax elements are missing.` |
| Value Hallucination | Predicted value is not part of system knowledge. | PRED: [{"@context": ["http://www.w3.org/ns/csvw", {"schema": "http://schema.org"}], "@id": "http://example.org/time-travel-media", "url": "time-travel-media.csv", "schema:description": "The table contains information about time travel media", "tableSchema": {"columns": [{"name": "year"}, {"name": "title"}, {"name": `"author"`}, {"name": `"short text description"`}, {"name": "category"}]}, "primaryKey": ["year", "title", "category"]}] → `System knowledge contains the columns "creators" and "description" but "author" and "short text description" are hallucinated.` |
| Property Excess | Predicted property is part of system knowledge but grounded too early. | PRED: [{"@context": ["http://www.w3.org/ns/csvw", {"schema": "http://schema.org"}], "@id": "http://example.org/football-records", "url": "football-records.csv", "schema:description": "The table contains information about football records", "tableSchema": {"columns": [{"name": `"record"`, "datatype": `"string"`}]}, "primaryKey": "record"}] → `Properties should have been grounded at a later stage.`|
|...|...|...|
