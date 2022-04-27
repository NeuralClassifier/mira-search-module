# Mira-Search-Module
The main goal here is to provide a fast, explainable, debuggable, semantic-based search engine for finding resources based on users' needs.

# Input
Users' intents will be recognized by the chatbot core module and the recognized intents will be given to the Mira search module as an input (e.g. Edmonton, depression).

# Output
The output will be the top 10 resources sorted by relevance to the input.

# Process
1) Translating or transforming input keywords into tags of resources. It is worth mentioning that our resources are tagged and stored in the Database. (e.g. input:need_doctor -> tags: doctor, psychologist, consultant)
2) Sometimes the keyword used is equal to one of the tags we have in the database. if not we will try to map each keyword at least to one tag. This will be done using three methods:
1.1) Direct mapping: using a lookup table we map a keyword to the synonyms in the tags (using our ontology)
1.2) Edit-distance mapping: edit distance will be used as a similarity measure to find similar tags (e.g. input: Alberta -> tag: Alberta-wide) this can also help tolerate user's spelling errors (e.g. input: red dear -> tag: red deer) 
1.3) Using Bert-based transformer and cosine similarity: all-MiniLM-L6-v2 model is used in order to vectorize the input and find the most semantically similar tag to it. Loading the model and transforming the user's input is timely on a limited server so we use this method only when previous methods did not find any tag for a single keyword.

3) Having keywords mapped to the predefined resource tags, we again use our ontology to relax the query (e.g. detected tag: Edmonton, relaxed tag: Alberta, Canada, worldwide)

4) Then we retrive all resources that they have at least one association with detected & relaxed tags. 
5) We have calculated TF-IDF weights for each resource and each tag and stored them in the database. Having that we rank resources based on the score they get for each tag.
In this phase, we also have some rules that can manipulate scores. for example, if a tag term is used in the title of a resource, that resource will be ranked higher. We also use BERT-based transformer model and cosine similarity to calculate the semantic similarity between resource titles and descriptions with tags.

5) Having scores for all related resources calculated then we sort them and send the top ten back to the chatbot.


# Ontology
you can find the ontology in the ontology folder it classified the tags of the portal in an excel sheet.
