# **About**
I received my B.S in Civil Engineering from the University of Maine in 2015, and spent the
last 5 years as a Nuclear Test Engineer for the United States Navy.

What most excites me the most is learning something new, and utilizing it to make the
world a better or more interesting place.

# **Personal Projects**
## [Learncentive](https://github.com/PetersWalker/learncentive)
An automated, tutoring web application.

### Technologies
Python, Flask, REST API, PosgreSQL, React

### Interesting Bits
This application contains a random math problem generator. Part of the challenge
here is generating random numbers in a time efficient way. A naive approach would
be to invoke python's randint function every time an integer is needed for an arithmetic
problem. For example:

```python
from random import randint

def generate_addition_problem():
    first = randint(1,10)
    second = randint(1,10)
    problem_string = '{}+{}'.format(first, second)
    return problem_string
```
Instead I approximated the distribution you would otherwise get when using randint
by caching a list of pre-generated random integers. These numbers can be used over
multiple Flask request contexts and accessed in the problem generator function using
a global index. The global index to access the cached list is initialized with a
random integer at the start of the request to ensure the integers that are used are
different with each request. In this way, the number of random numbers actually
generated per request is reduced to once per Flask request context, regardless of
the amount of problems requested:

```python
from learncentive.problem_generation import config
index = config.cache_index

def generate_addition_problem():
    first, second = _get_integers_from_cache(values_needed=2, cache_index=index)
    problem_string = '{}+{}'.format(first, second)
    return problem_string
```
Here the get_integers_from_cache function also increments the cache index

# **Curiosities**
## [Langdict](https://github.com/PetersWalker/langdict)
Transforming lexicons into acyclic graphs.
## Technologies
Python, NetworkX
## Background
After a trip to Italy in 2018 I heard my American friend speak fluent Italian, and
learn new Italian words while doing so on the fly. This made me wonder what the minimum
required vocabulary to be fluent in a language is. As a fluent speaker of a language
if you want to understand a new word, you can consult a dictionary like Merriam Webster,
and read out a definition from within the context of your own language. The definition
is made up of words that you may or may not know the definition of themselves. You
in turn must look up these other words until all definitions are resolved by the
words in your current vocabulary.

The natural implication to this is that there must be a set of words that are basic
to the lexicon of a language, from which all other words definitions can be derived.

I chose to explore this using a data model that is a natural fit: a directed graph.
Each word in a language dictionary is a vertex, with its directed edges pointing
to the vertices (words) that it defines. For example, the word "the" as a vertex
points to many other words as it's used in many definitions.

After obtaining an english dictionary, cleaning the data, and loading it into a NetworkX
Digraph, what is left is a directed graph with many cycles. Each cycle is a circular
definition, which is exactly the pattern we are looking for. Finding all
the words that are contained in cycles, and combining those cycles into multi-word vertices,
will produce a set of words that are circularly defined by each other (see [strongly connected components](https://en.wikipedia.org/wiki/Strongly_connected_component)), and that define
all other words in the dictionary. In other words, we simply need to transform this
directed cyclic graph, into an [acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph).
The Python library NetworkX thankfully has builtins for this specific purpose:

```python
g = nx.DiGraph()
nx.from_dict_of_lists(dict, create_using=g)
g = g.reverse()

scc = nx.strongly_connected_components(g)
G = nx.condensation(g, scc=scc)
```
The result is an acyclic graph with the first vertex being a set of words that
can be used to define all others.

## Interesting bits

I found that the particular dictionary used is very important. For example, the
dictionary used in the langdict repository was The Online Plain Text English Dictionary.
The number of definitions for each word and their long lengths made for a graph that didn't
condense enough to draw strong conclusions from the dataset.

Here is it's definition of *Sea* among 4 other definitions:

  *"Sea (n.) An inland body of water, esp. if large or if salt or brackish; as, the Caspian Sea; the Sea of Aral; sometimes, a small fresh-water lake; as, the Sea of Galilee."*

Whereas the definition for *Sea* from the Simple English Wiktionary is:

  *"Sea (n.) A place with a large amount of salt water.""*

In the future, wrangling data from a more concise dictionary would be a better choice.
