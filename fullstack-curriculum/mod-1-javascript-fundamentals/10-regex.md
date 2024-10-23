# Lecture: Intro to RegEx

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-2-2-regex)!
{% endhint %}


**Table of Contents:**
- [Slides](#slides)
- [What is a Regular Expression](#what-is-a-regular-expression)
- [How to Regular Expressions in JavaScript](#how-to-regular-expressions-in-javascript)
  - [`RegExp.test(str)` and the `i` flag](#regexpteststr-and-the-i-flag)
  - [`str.search(RegExp)`](#strsearchregexp)
  - [`str.match(RegExp)` and the `g` flag](#strmatchregexp-and-the-g-flag)
  - [Flags](#flags)
  - [`str.replace(RegExp, replacement)`](#strreplaceregexp-replacement)
- [Regular Expression Syntax](#regular-expression-syntax)
  - [There are a lot of cool ways to use regular expressions](#there-are-a-lot-of-cool-ways-to-use-regular-expressions)
  - [Special Characters](#special-characters)


## Slides

{% embed url="https://docs.google.com/presentation/d/1KNxfevZGXU2srFOejf6qBgOwXznH8ddQ3ePeqTXbGiQ/embed?start=false&loop=false&delayms=3000" %}

## What is a Regular Expression

> A **regular expression** (or "Reg Ex") is a sequence of characters that specifies a "match pattern" to search for strings in text, extract information, or validate input.

To create a regular expression, we use a pair of forward slashes with the match pattern inside. A regular expression can also be created using the `new RegExp()` constructor.

In JavaScript, a regular expression is a type of object.

```js
// can be used to search for the sequence of characters "cat"
const catRegEx = /cat/  
// can be used to search for the sequence of characters "dog"
const dogRegEx = new RegExp('dog');

console.log(typeof catRegEx, typeof dogRegEx);
// object object
```

## How to Regular Expressions in JavaScript

The methods to be aware of that utilize regular expressions are:
1. `RegExp.test(str)` - validate that a string has a match
2. `str.search(RegExp)` - find the index of a match in a string
3. `str.match(RegExp)` - find one or more matches in a string
4. `str.replace(RegExp, replacement)` - replace one or more matches in a string

> Note that the first method is invoked on a regular expression and the input is a string while the other two are invoked on a string and the input is a regular expression.

### `RegExp.test(str)` and the `i` flag

The `RegExp.test(str)` returns `true` if there is a match between the regular expression and the given string.

```js
const catRegEx = /cat/ 

catRegEx.test("the cat in the hat"); // Returns true
catRegEx.test("the dog in the hat"); // Returns false because no "cat"
catRegEx.test("the Cat in the hat"); // Returns false because of case sensitivity
```

By default, regular expressions are case sensitive and, unless otherwise specified, must be an exact match.

Adding the `i` flag after the second `/` allows for case-**insensitive** matches:

```js
// Add the i flag after the second /
const catRegEx = /cat/i 

catRegEx.test("the cat in the hat"); // Returns true
catRegEx.test("the Cat in the hat"); // Also returns true
```

### `str.search(RegExp)`

`str.search(RegExp)` returns the index of the first match between the string and the given regular expression, or `-1` if none exist.

```js
const phrase = 'How now brown cow?'
phrase.search(/brow/);  // Returns 8
phrase.search(/ow/);    // Returns 1
```

### `str.match(RegExp)` and the `g` flag

`str.match(RegExp)` returns an array containing matches between the string and the given regular expression. 

By default, regular expressions only match the first match. 

```js
const phrase = 'How now brown cow?'
const firstMatch = phrase.match(/ow/);

// Without the global flag, match will only return the first match
console.log(firstMatch[0]);      // Prints 'ow'
console.log(firstMatch.length);  // Prints 1
```

Adding the global `g` flag after the second `/` causes `match` to return ALL matches.

```js
// With the global flag:
const allMatches = phrase.match(/ow/g);
console.log(allMatches);        // Prints ['ow', 'ow', 'ow', 'ow'];
console.log(allMatches.length); // Prints 4
```

### Flags

Flags go after the second forward slash and alter the behavior of the regular expression. They can be combined.

* The `g` flag make the regular expression **global**, meaning it will return ALL matches between a string and regular expression if possible. 
* The `i` flag makes the regular expression case **insensitive**

This is particularly useful with the `str.match(RegExp)` method:

```js
const phrase = "my cat is named Catherine";

// match all "cat"s regardless of case, not just the first one
const allCats = phrase.match(/cat/gi); 

console.log(allCats); // Prints ['cat', 'Cat']
```

### `str.replace(RegExp, replacement)`

`str.replace(RegExp, replacement)` replaces matches between the string the given regular expression with the given `replacement` string. 

```js
const phrase = "my cat is named Catherine";

// Replace only the first "cat", regardless of case
const newPhrase = phrase.replace(/cat/i, 'dog');

console.log(newPhrase); // Prints 'my dog is named dogherine'
```

By default, `replace` will only replace the first match. You can use the `g` flag to replace ALL matches.

```js
const phrase = "my cat is named Catherine";

// Replace all "cat"s regardless of case, not just the first one
const newPhrase = phrase.replace(/cat/gi, 'dog');

console.log(newPhrase); // Prints 'my dog is named dogherine'
```

## Regular Expression Syntax

### There are a lot of cool ways to use regular expressions

Here are some snippets to get started! 

| **Name**                   | **Info**                              | **Example**        |
| -------------------------- | ------------------------------------- | ------------------ |
| Flags                      | g=global, I=insensitive               | /cat/gi            |
| letters                    | You know letters                      | /dog/              |
| digits                     | You know digits                       | /12/               |
| Character Set/Class        | Brackets                              | /[aeiou]/          |
| Negated Character Set      | Carrot and brackets                   | /[^aeiou]/         |
| O or more                  | *                                     | /a*/               |
| 1 or more                  | +                                     | /a+/               |
| 0 or 1                     | ?                                     | /a?/               |
| Or                         | \|                                    | /cat\|dog/         |
| Start of string            | Carrot, no brackets                   | /^hi/              |
| End of string              | Dollar sign                           | /bye$/             |
| Quantifier exactly         | Braces, one number                    | /AH{10}/           |
| Quantifier at least X many | Braces, one number and comma          | /OH{3,}/           |
| Quantifier exact range     | Braces, two comma separated numbers   | /WO{2,4}W/         |
| Groups                     | Parens with optional pipe OR operator | /(my)\s(cat\|dog)/ |


### Special Characters 

| Character Class | Definition                    |
| --------------- | ----------------------------- |
| [0-9]           | Any single digit              |
| [3-30]          | And number in range           |
| [a-z]           | Any lowercase letter          |
| [a-d]           | Any lowercase letter in range |
| [A-Z]           | Any uppercase letter          |
| [E-Q]           | Any uppercase letter          |
| [a-zA-Z]        | Any letter                    |
| \d              | Any single digit              |
| \D              | Any NON digit                 |
| \w              | Any alphanumeric (and _)      |
| \W              | Any NON alphanumeric          |
| \s              | Any whitespace character      |
| \S              | Any NON whitespace character  |
| \b              | Any word break                |
| \B              | Any NON word break            |
| .               | Any character at all          |
| \\.             | An escaped period             |