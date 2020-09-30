# Personal Projects
## Learncentive
#### An automated, tutoring web application.  
### **Technologies**
Python, Flask, PosgreSQL, React
### **Interesting Bits**
Part of the challenge in writing this application is generating random numbers
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
