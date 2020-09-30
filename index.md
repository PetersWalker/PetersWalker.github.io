# Personal Projects
## [Learncentive](https://github.com/PetersWalker/learncentive)
An automated, tutoring web application.

### Technologies
Python, Flask, PosgreSQL, React
### Interesting Bits
Part of the challenge in writing this application was generating random numbers
for math problems in a time efficient way. A naive approach would be to invoke
python's randint function every time an integer is needed for an arithmetic problem:
```python
from random import randint

def generate_addition_problem():
    first = randint(1,10)
    first = randint(1,10)
    problem_string = '{}+{}'.format(first, second)
    return problem_string
```
Instead I approximated the distribution you would otherwise get when using randint
by caching a list of random integers to be used over multiple Flask request contexts
and accessing that list in the problem generator function using a global index. the
current global index is initialized using a random number for every request context
in this way, the number of random numbers actually generated per request is reduced
to once per Flask request context, regardless of the amount of problems requested:

```python
from learncentive.problem_generation import config
index = config.cache_index

def generate_addition_problem():
    first, second = _get_integers_from_cache(values_needed=2, cache_index=index)
    problem_string = '{}+{}'.format(first, second)
    return problem_string

```
Here the get_integers_from_cache function also increments the cache index

# Curiosities
## [Langdict](https://github.com/PetersWalker/langdict)
Transforming lexicons into acyclic graphs.
## Technologies
Python, NetworkX
## Background
I was wondering what the minimum required vocabulary for a language is. As an
english speaker if you want to learn a new word, you consult a dictionary like
Merriam Webster and read out a definition. The definition is made up of words that
you may or may not know the definition of themselves. You in turn must look up these
other words until all definitions are resolved by the words in your current vocabulary.
The natural implication is that there must be a set of words that are basic to the
lexicon of a language, and which all other words can be defined by on one level or
another.

The model that works best to understand the properties of these words is a directed
graph with each word being a node, and linked to the words that define it.

In order to find those specific words that are fundamental to the vocabulary, we need
to transformthis graph into an [acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph). The most fundamental words that can be used to construct all other definitions and will be at the beginning of this acyclic graph.
