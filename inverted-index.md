---
layout: page
title: Inverted Index
---

An `InvertedIndex` is an index that considers the inserted documents as a set of tokens that are all keys that one can use to retrieve the documents.

```js
var InvertedIndex = require('mnemonist/inverted-index');
```

## Use case

Usually, `InvertedIndex` are used to build full-text search engines.

Here is how it works:

1. Transform the inserted document into a set of tokens.
2. For each token, add an entry to an internal map with the key being the token & the value being the document.

Let's see how we could search for documents using a very naive word tokenizer:

```js
// The `words` function from lodash is pretty good, for instance
var words = require('lodash/words');

var documents = [
  'The mouse is a gentle animal',
  'The cats eats the mouse',
  'The mouse eats cheese'
];

// Creating our inverted index with our `words` tokenizer function
var index = new InvertedIndex(words);

// Now we can query it
index.get('mouse');
>>> [
  'The mouse is a gentle animal',
  'The mouse eats cheese'
]

index.union('cats cheese');
>>> [
  'The cats eats the mouse',
  'The mouse eats cheese'
]
```

If you need more to retrieve more information about the indexed documents such as occurrences, positions etc., check out the [`SearchIndex`]({{ site.baseurl }}/search-index).

## Constructor

The `InvertedIndex` either takes a single argument being a tokenizer function that will process both inserted items or keys & the queries; or a tuple containing two tokenizer functions, one for the inserted items or keys and the second one for the queries.

*Example with one tokenizer function*

```js
// Let's create an index using a single hash function:
var index = new InvertedIndex(function(value) {
  return words(value);
});

// Then you'll probably use #.set to insert items
index.set(movie.title, movie);
index.get(queryTitle);
```

*Example with two tokenizer functions*

```js
// Let's create an index using two different hash functions:
var index = new Index([
  
  // Tokenizer function for inserted items:
  function(movie) {
    return words(movie.title);
  },

  // Tokenizer function for queries
  function(query) {
    return words(query);
  }
]);

// Then you'll probably use #.add to insert items
index.add(movie);
index.get(queryTitle);
```

### Static #.from

Alternatively, one can build an `InvertedIndex` from an arbitrary JavaScript iterable likewise:

```js
var index = InvertedIndex.from(list, tokenizer [, useSet=false]);
var index = InvertedIndex.from(list, tokenizers [, useSet=false]);
```

## Members

* [#.size](#size)

## Methods

*Mutation*

* [#.add](#add)
* [#.set](#set)
* [#.clear](#clear)

*Read*

* [#.get, #.intersection](#get)
* [#.union](#union)

### #.size

Number of documents stored in the index.

```js
var index = new InvertedIndex(words);

index.add('The cat eats the mouse.');

index.size
>>> 1
```

### #.add

Tokenize the given document using the relevant function and adds it to the index.

```js
var index = new InvertedIndex(words);

index.add('The cat eats the mouse.');
```

### #.set

Tokenize the given key using the relevant function and add the given document to the index.

```js
var index = new InvertedIndex(words);

var doc = {text: 'The cat eats the mouse.', id: 34}

index.set(doc.text, doc);
```

### #.clear

Completely clears the index of its documents.

```js
var index = new InvertedIndex(words);

index.add('The cat eats the mouse.');
index.clear();

index.size
>>> 1
```

### #.get, #.intersection

Tokenize the query using the relevant function, then retrieves the intersection of documents containing the resulting tokens.

```js
var index = new InvertedIndex(words);

index.add('The cat eats the mouse.');
index.add('The mouse eats cheese.');

index.get('mouse');
>>> [
  'The cat eats the mouse.',
  'The mouse eats cheese.'
]

index.get('cat mouse');
>>> [
  'The cat eats the mouse.'
]
```

### #.union

Tokenize the query using the relevant function, then retrieves the union of documents containing the resulting tokens.

```js
var index = new InvertedIndex(words);

index.add('The cat eats the mouse.');
index.add('The mouse eats cheese.');

index.get('mouse');
>>> [
  'The cat eats the mouse.',
  'The mouse eats cheese.'
]

index.get('cat mouse');
>>> [
  'The cat eats the mouse.',
  'The mouse eats cheese.'
]
```