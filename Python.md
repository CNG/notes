This is not a complete reference, as I started making these notes while reading "Learning Python" by Mark Lutz but then shifted to making flashcards in my "[technical](https://github.com/CNG/anki-decks/tree/master/General__Technical)" Anki deck.

# print

Python 2:

    print 'Python', python_version()
    print 'Hello, World!'
    print('Hello, World!')
    print "text", ; print 'print more text on the same line'

Python 3:

    print('Python', python_version())
    print('Hello, World!')
    print("some text,", end="")
    print(' print more text on the same line')

# Data types

* Numbers: `1234, 3.1415, 3+4j, 0b111, Decimal(), Fraction()`
* Strings: `'spam', "Bob's", b'a\x01c', u'sp\xc4m'`
* Lists: `[1, [2, 'three'], 4.5], list(range(10))`
* Dictionaries: `{'food': 'spam}`
* Tuples: `(1, 'spam, 4), tuple('spam'), namedtuple`
* Sets: `set('abc'), {'a', 'b', 'c'}`
* Other: Booleans, types, `None`
* Program units: Functions, modules, classes
* Implementation related: Compiled code, stack tracebacks

* **immutable**: numbers, strings, tuples
* **mutable**: lists, dictionaries, sets

## strings

`'abc'` or `"abc"` or `"""a
bc"""` or `'''ab
c'''` or `r'C:\text\new'`

### sequence operations

    S = 'Spam'
    len(S)
    S[0]
    S[-1] == S[len(S)-1]
    S[:-1] #  everything but last
    S[1:3], S[1:], S[:3], S[:]
    S + 'xyz' #  concatenation
    S * 8 #  repetition

Expand to list and change (can also use bytearray):

    S = 'shrubbery'
    L = list(S)
    L[1] = 'c'
    ''.join(L)

### type specific methods

    S = 'spam'
    S.find('pa')
    S.replace('pa', 'XYZ')
    S.split(',')
    S.upper(), S.isalpha(), S.isdigit(), S.rstrip(), etc.
    '{0} and {1}'.format('spam', 'eggs') # 2.6+, 3.0+
    '{} and {}'.format('spam', 'eggs') # 2.7+, 3.1+

### help

`dir()`, `dir(s)`, `help(s)`, `help(s.replace)`

## lists

Same sequence operations as springs but return lists, not strings.

    L.append('NI')
    L.pop(2) # zero based, or can use equiv del statement
    M.sort()
    M.reverse()

Out of bounds reference is IndexError

### comprehensions

Can iterate over any iterable object.

    col2 = [row[1] for row in M]
    [row[1] for row in M if row[1] % 2 == 0]

With parentheses can create generators:

    G = (sum(row) for row in M)
    next(G) # iter(G) not required
    list(map(sum, M)) # return all at once as list: [6, 15, 24]

Create sets and dictionaries:

    {sum(row) for row in M} # {24, 6, 15}
    {i: sum(M[i]) for i in range(3)} # {0: 6, 1: 15, 2: 24}
    
## dictionaries

    D = {}
    D['name'] = 'Bob'
    D['job'] = dev
    D # {'name': 'Bob', 'job': 'dev'}
    D = dict(name='Bob', job='dev')
    D = dict(zip(['name', 'job'], ['Bob', 'dev']))

Fetching nonexistent is KeyError. To avoid:

    'f' in D #  false
    if not 'f' in D:
        print('missing')
    # get method
    value = D.get('x', 0)
    value = D['x'] if 'x' in D else 0

Sorting can be done in one step with newer `sorted` built in function, otherwise three steps in older Pythons:

    for key in sorted(D):
        print(key, '=>', D[key])
    Ks = list(D.keys()) #  unordered keys list
    Ks = Ks.sort() #  sorted keys list
    for key in Ks:
        print(key, '=>', D[key])

## tuples

Like lists but immutable, usually used for fixed collections like components of a calendar date. One item tuples require trailing comma `T = (2, )`. Parentheses can be omitted in contexts where commas don't otherwise matter; the comma actually builds the tuple: `T = 'spam', 3.0`.

## files

File contents are always strings.

    f = open('data.txt', 'w')
    f.write('Hello\n')
    f.close()
    f = open('data.txt') # 'r' is default
    text = f.read() # read entire file into string
    
    for line in open('data.txt'): print(line)
    
    import struct
    packed = struct.pack('>i4sh', 7, b'spam', 8) # `b` not required in 2.X
    file = open('data.bin', 'wb')
    file.write(packed)
    file.close()
    data = open('data.bin', 'rb').read()
    data[4:8] # slice bytes in the middle
    list(data) # sequence of 8 bit bytes
    struct.unpack('>i4sh', data)
    
    file = open('unidata.txt', 'w', encoding='utf-8')

## sets

Support set operations:

* `X & Y` # intersection (set)
* `X | Y` # union (set)
* `X - Y` # difference (set)
* `X > Y` # superset (Boolean)

Other:

    list(set([1, 2, 1, 3, 1])) # filter out duplicates, possibly reordered
    set('spam') - set('ham') # finding differences in collections ({'p', 's'})
    set('spam') == set('asmp') # order neutral equality
    
## types

`type(L)`, `type(type(L))`:

* 2.X: `<type 'list'>, <type 'type'>`
* 3.X: `<class 'list'>, <class 'type'>` (types and classes merged in 3.X)

Checks:

    if type(L) == type([]):
    if type(L) == list:
    if isinstance(L, list):



# in

    'p' in set('spam')
    'p' in 'spam'
    'ham' in ['eggs', 'spam', 'ham']

# if

1. full form: `if`, `elif`, `else`
2. ternary expression `if`/`else`
3. `if` comprehension filter

# iteration

Object is iterable if it supports the iteration protocol: they respond to `iter` call with an object that advances in response to `next` calls and raises an exception when finished producing values. Usually iterable if it is either a physically stored sequence in memory or an object that generates one item at a time in the context of an iteration operation, a sort of virtual sequence.

generator comprehension, file objects, and in 3.X includes core tools like `range` and `map`


















# performance

`time` and `timeit` modules for timing speed of alternatives, and the `profile` module for isolating bottlenecks


