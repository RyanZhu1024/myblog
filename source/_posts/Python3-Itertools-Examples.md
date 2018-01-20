---
title: Python3 Itertools Examples
date: 2018-01-20 14:33:55
tags: Python
---



```python
import itertools
transportations = ['car', 'ship', 'flight', 'train']
colors = ['red', 'blue', 'yellow', 'green']
animals = ['cat', 'dog', 'mouse', 'horse', 'lion', 'tiger', 'whale']
team_members = [
    {'name': 'baobao1', 'leader': 'ryan'}, 
    {'name': 'baobao2', 'leader':'ryan'}, 
    {'name': 'baobao3', 'leader': 'shasha'},
    {'name': 'queque', 'leader': 'shasha'},
    {'name': 'laopo', 'leader': 'laogong'}
]
numbers = [1,2,3,4,5,6,7,8,9]
```

## accumulate(data, func)

```python
result = itertools.accumulate(numbers)
print(list(result))
```

```
[1, 3, 6, 10, 15, 21, 28, 36, 45]
```



```python
result = itertools.accumulate(numbers, lambda x, y: min(x, y))
print(list(result))
```

```
[1, 1, 1, 1, 1, 1, 1, 1, 1]
```



```python
result = itertools.accumulate(numbers, lambda x, y: x * y)
print(list(result))
```

```
[1, 2, 6, 24, 120, 720, 5040, 40320, 362880]
```



## combinations(data, r) ==> C(n, r)

```python
result = itertools.combinations(transportations, 2)
print(list(result))
```

```
[('car', 'ship'), ('car', 'flight'), ('car', 'train'), ('ship', 'flight'), ('ship', 'train'), ('flight', 'train')]
```



```python
result = itertools.combinations(colors, len(colors))
print(list(result))
```

```
[('red', 'blue', 'yellow', 'green')]
```



## combinations_with_replacement(data, r) C(n ,r) where same item can be resued

```python
result = itertools.combinations_with_replacement(colors, len(colors))
print(list(result))
```

```
[('red', 'red', 'red', 'red'), ('red', 'red', 'red', 'blue'), ('red', 'red', 'red', 'yellow'), ('red', 'red', 'red', 'green'), ('red', 'red', 'blue', 'blue'), ('red', 'red', 'blue', 'yellow'), ('red', 'red', 'blue', 'green'), ('red', 'red', 'yellow', 'yellow'), ('red', 'red', 'yellow', 'green'), ('red', 'red', 'green', 'green'), ('red', 'blue', 'blue', 'blue'), ('red', 'blue', 'blue', 'yellow'), ('red', 'blue', 'blue', 'green'), ('red', 'blue', 'yellow', 'yellow'), ('red', 'blue', 'yellow', 'green'), ('red', 'blue', 'green', 'green'), ('red', 'yellow', 'yellow', 'yellow'), ('red', 'yellow', 'yellow', 'green'), ('red', 'yellow', 'green', 'green'), ('red', 'green', 'green', 'green'), ('blue', 'blue', 'blue', 'blue'), ('blue', 'blue', 'blue', 'yellow'), ('blue', 'blue', 'blue', 'green'), ('blue', 'blue', 'yellow', 'yellow'), ('blue', 'blue', 'yellow', 'green'), ('blue', 'blue', 'green', 'green'), ('blue', 'yellow', 'yellow', 'yellow'), ('blue', 'yellow', 'yellow', 'green'), ('blue', 'yellow', 'green', 'green'), ('blue', 'green', 'green', 'green'), ('yellow', 'yellow', 'yellow', 'yellow'), ('yellow', 'yellow', 'yellow', 'green'), ('yellow', 'yellow', 'green', 'green'), ('yellow', 'green', 'green', 'green'), ('green', 'green', 'green', 'green')]
```



## groupby(iterable, key=None)

```python
result = itertools.groupby(team_members, key = lambda member: member['leader'])
for leader, group in result:
    print(leader)
    print(list(group))
```

```
ryan
[{'name': 'baobao1', 'leader': 'ryan'}, {'name': 'baobao2', 'leader': 'ryan'}]
shasha
[{'name': 'baobao3', 'leader': 'shasha'}, {'name': 'queque', 'leader': 'shasha'}]
laogong
[{'name': 'laopo', 'leader': 'laogong'}]
```

## permutations(iterable, r=None) P(n, r)

```python
result = itertools.permutations(animals, 2)
print(list(result))
```

```
[('cat', 'dog'), ('cat', 'mouse'), ('cat', 'horse'), ('cat', 'lion'), ('cat', 'tiger'), ('cat', 'whale'), ('dog', 'cat'), ('dog', 'mouse'), ('dog', 'horse'), ('dog', 'lion'), ('dog', 'tiger'), ('dog', 'whale'), ('mouse', 'cat'), ('mouse', 'dog'), ('mouse', 'horse'), ('mouse', 'lion'), ('mouse', 'tiger'), ('mouse', 'whale'), ('horse', 'cat'), ('horse', 'dog'), ('horse', 'mouse'), ('horse', 'lion'), ('horse', 'tiger'), ('horse', 'whale'), ('lion', 'cat'), ('lion', 'dog'), ('lion', 'mouse'), ('lion', 'horse'), ('lion', 'tiger'), ('lion', 'whale'), ('tiger', 'cat'), ('tiger', 'dog'), ('tiger', 'mouse'), ('tiger', 'horse'), ('tiger', 'lion'), ('tiger', 'whale'), ('whale', 'cat'), ('whale', 'dog'), ('whale', 'mouse'), ('whale', 'horse'), ('whale', 'lion'), ('whale', 'tiger')]
```



## product(iter1, iter2). 2 by 2 pair

```python
result = itertools.product(transportations, colors)
print(list(result))
```

```
[('car', 'red'), ('car', 'blue'), ('car', 'yellow'), ('car', 'green'), ('ship', 'red'), ('ship', 'blue'), ('ship', 'yellow'), ('ship', 'green'), ('flight', 'red'), ('flight', 'blue'), ('flight', 'yellow'), ('flight', 'green'), ('train', 'red'), ('train', 'blue'), ('train', 'yellow'), ('train', 'green')]
```



```python
result = itertools.product(transportations, animals)
print(list(result))
```

```
[('car', 'cat'), ('car', 'dog'), ('car', 'mouse'), ('car', 'horse'), ('car', 'lion'), ('car', 'tiger'), ('car', 'whale'), ('ship', 'cat'), ('ship', 'dog'), ('ship', 'mouse'), ('ship', 'horse'), ('ship', 'lion'), ('ship', 'tiger'), ('ship', 'whale'), ('flight', 'cat'), ('flight', 'dog'), ('flight', 'mouse'), ('flight', 'horse'), ('flight', 'lion'), ('flight', 'tiger'), ('flight', 'whale'), ('train', 'cat'), ('train', 'dog'), ('train', 'mouse'), ('train', 'horse'), ('train', 'lion'), ('train', 'tiger'), ('train', 'whale')]
```



## zip and zip_longest

```python
result = zip(colors, transportations)
print(list(result))
```

```
[('red', 'car'), ('blue', 'ship'), ('yellow', 'flight'), ('green', 'train')]
```



```python
result = zip(colors, animals)
print(list(result))
```

```
[('red', 'cat'), ('blue', 'dog'), ('yellow', 'mouse'), ('green', 'horse')]
```



```python
result = itertools.zip_longest(colors, transportations)
print(list(result))
```

```
[('red', 'car'), ('blue', 'ship'), ('yellow', 'flight'), ('green', 'train')]
```



```python
result = itertools.zip_longest(colors, animals)
print(list(result))
```

```
[('red', 'cat'), ('blue', 'dog'), ('yellow', 'mouse'), ('green', 'horse'), (None, 'lion'), (None, 'tiger'), (None, 'whale')]
```

