Retrieval Augmented Generation
==============================

This programming excecise explores using retrieval augmented generation (RAG) to ground large language models to help generate factual responses and prevent hallucinations. This experiences is targetted toward folks who are extremely new to Python and programming and has two distinct learning objectives:

1. Gain familiarity with commonly used string methods
2. Develop a basic understand of retrieval augmented generation for LLMs

Language Modelling
------------------

Language modelling is the task of predicting one comes next in a language utterance. 

For example, consider the phrace "They ran to a local". A language model could [compute probabilities](https://exbert-project-exbert.hf.space/client/exBERT.html?model=gpt2&modelKind=autoregressive&sentence=They%20ran%20to%20a%20local&layer=11&heads=..0,1,2,3,4,5,6,7,8,9,10,11&threshold=0.7&tokenInd=null&tokenSide=null&maskInds=..&hideClsSep=true) of the next word in this phrase as:

| Word       | Probability |
| ---------- | ----------- |
| hospital   | 0.06        |
| church     | 0.03        |
| police     | 0.02        |
| restaurant | 0.02        |
| grocery    | 0.02        |

These probabilities can then be used for many tasks including compression and generation. In this case (assuming greedy sampling) the model would generate "hospital" as the next word in the sequence.

Language Models in Python
-------------------------

There are numerous Python packages available for interacting with language models. One of the simplest for new learners is the [languagemodels](https://pypi.org/project/languagemodels/) package. It can be installed simply using the following command in your system shell given that you already have Python 3 and pip installed properly:

```
pip install languagemodels
```

We can then use this package to create simple LLM completions for instruction-oriented prompts:

```python
>>> import languagemodels
>>> languagemodels.do("Answer the question: What is the capital of France?")
'Paris.'
```

Hallucinations
--------------

Unfortunately, for a variety of reasons large language models may not always answer truthfully. The relatively small model running locally via `languagemodels` package will commonly generate inaccurate results (sometimes called "hallicinations") even for relatively simple prompts. For example:

```python
>>> import languagemodels
>>> languagemodels.do("Answer the question: What are the names of Earth's 3 moons?")
'The names of Earth's 3 moons are: 1. Ganymede 2. Scorpius 3. Neptune'
```

Hallucinations can also occur when attempting to prompt a model for specific details. For example:

```python
>>> import languagemodels
>>> languagemodels.do("Answer the question: Who created Python?")
'The creator of Python is Tim Berners-Lee.'
```

Providing Context
-----------------

One way to address hallucinations is to provide factual context in the LLM prompt. We can then prompt the model to answer based on that context:

```python
>>> import languagemodels
>>> languagemodels.do("Answer from context: Who created Python? Context: Guido created Python.")
'Guido created Python'
```

That may not seem useful, as we have to know the answer to our question in order to have it answered, but this technique can be used to with documents to provide grounded answers from the document. Consider the following example where we use a helper function to context from Wikipedia:

```python
>>> import languagemodels
>>> python_wiki = languagemodels.get_wiki("Python")
>>> python_wiki
'Python is a high-level, general-purpose programming language...'
>>> languagemodels.do(f"Answer from context: Who created Python? Context: {python_wiki}")
'Guido van Rossum created Python.'
```

This demonstrates how we can use retrieval from a set of documents to provide knowledge and grounding to large language models.

Task
----

Create a chatbot that can answer very basic questions about topics found in questions. This should be accomplished using retrieval-augmented generation as described above.

Note that current language models are limited in the amount of context that they can handle. This limit exists for most current models, and the simple models used here can only handle a few hundred words of context. In addition, generation will take longer for longer prompts. This means that **you should only have one context document supplied for each user question**.

Specifically, your application should be able to correctly answer the following questions:

- Who created Python? (Guido van Rossum)
- How many moons does Saturn have? (146)
