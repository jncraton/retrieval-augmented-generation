Retrieval Augmented Generation
==============================

This programming exercise explores using [retrieval augmented generation](https://arxiv.org/abs/2005.11401) (RAG) to ground large language models to help generate factual responses and prevent hallucinations. This experience is targeted toward learners who are new to Python programming and has two distinct learning objectives. After students complete this project, they should be able to:

1. Use strings in Python as discussed in [PY4E Chapter 6](https://www.py4e.com/html3/06-strings)
2. Describe the purpose of retrieval augmented generation for LLMs

Language Modeling
------------------

Language modeling is the task of predicting what word comes next in a language utterance. 

For example, consider the phrase "They ran to a local". A language model could [compute probabilities](https://exbert-project-exbert.hf.space/client/exBERT.html?model=gpt2&modelKind=autoregressive&sentence=They%20ran%20to%20a%20local&layer=11&heads=..0,1,2,3,4,5,6,7,8,9,10,11&threshold=0.7&tokenInd=null&tokenSide=null&maskInds=..&hideClsSep=true) of the next word in this phrase as:

| Word       | Probability |
| ---------- | ----------- |
| hospital   | 0.06        |
| church     | 0.03        |
| police     | 0.02        |
| restaurant | 0.02        |
| grocery    | 0.02        |

These probabilities can then be used for many tasks including compression and generation. For the above prompt, a generative model would generate "hospital" (assuming greedy sampling) as the next word in the sequence.

Language Models in Python
-------------------------

Developing a deep understanding of the inner workings of modern large language models (LLMs) is out of the scope of this assignment, but we are still able to prompt them to create a generative output.

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

Unfortunately, large language models may not always answer in a factually correct manner. The relatively small model running locally via the `languagemodels` package will commonly generate inaccurate results (sometimes called "hallucinations") even for relatively simple prompts. For example:

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

This time, the model simply returned a confabulated answer to the question. For those unaware, [Tim Berners-Lee](https://en.wikipedia.org/wiki/Tim_Berners-Lee) is perhaps best known for inventing the World Wide Web, while the Python programming language was created by [Guido van Rossum](https://en.wikipedia.org/wiki/Guido_van_Rossum).

Providing Context
-----------------

One way to address hallucinations is to provide factual context in the LLM prompt. We can then prompt the model to answer based on that context:

```python
>>> import languagemodels
>>> languagemodels.do("Answer from context: Who created Python? Context: Guido created Python.")
'Guido created Python'
```

That may not seem useful, as we have to know the answer to our question in order to have it answered, but this technique can be used to with larger documents to provide grounded answers from the document. Consider the following example where we use a helper function to get context from Wikipedia:

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

Create a chatbot that can answer very basic questions about topics found within the questions. This should be accomplished using retrieval-augmented generation as described above.

Handout code is provided to create a structure for the project in [rag.py](rag.py). You should complete the following within the handout code:

1. Complete the `get_topic` function as described in the function's docstring.
2. Complete the `create_rag_prompt` function as described in the function's docstring.
3. Complete the `main` function following the inline TODO comments.

A complete run of your program should look something like this:

```
Assistant: Please enter a question for me to answer.
User: What is Python?
Assistant: Python is a high-level, general-purpose programming language that emphasizes code readability with the use of significant indentation. It supports multiple programming paradigms and is often described as a "batteries included" language due to its comprehensive standard library.
```

Testing
-------

When writing programs to solve multi-step problems, it is often helpful to break the program down into steps using functions as we have done in the handout code here. This creates more readable code, but it also allows us to test our solutions to the subproblems without testing the entire program. Some basic tests are included in the `main` function to demonstrate that `get_topic` and `create_rag_prompt` work correctly before using them. Feel free to add, change, or remove these tests as you develop your program to help you get these functions working correctly.

Once your program is complete, your application should be able to correctly answer the following questions:

- Who created Python? (Guido van Rossum)
- How many moons does Saturn have? (146)

Resources
---------

This lab is expected to make use of string functions, formatting, indexing, and/or slicing. The following resources may be helpful to learn more about these topics:

- [PY4E Strings Chapter](https://www.py4e.com/html3/06-strings)
- [Formatted String Literals Documentation](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)
- [str.strip](https://docs.python.org/3/library/stdtypes.html#str.strip)
- [str.split](https://docs.python.org/3/library/stdtypes.html#str.split)
- [str.title](https://docs.python.org/3/library/stdtypes.html#str.title)
- [String slicing syntax from SO](https://stackoverflow.com/a/509295)

Additional Exploration
----------------------

Once you have a working program, there are few other things you could try to get a deeper understanding of these topics.

1. Enhance `get_topic` to perform more robust topic search. It can currently identify single word proper nouns such as "Python", but it won't work properly on longer proper nouns such as "Taylor Swift". Try to enhance this function to make it more robust.
2. Explore different prompt formats in `create_rag_prompt`. The formatting and language used in a prompt will change the way a model responds. Experiment with other prompt format and observe how performance changes.