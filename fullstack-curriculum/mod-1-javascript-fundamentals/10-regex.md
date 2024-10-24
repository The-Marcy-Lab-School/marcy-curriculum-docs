# Lecture: Intro to RegEx

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-2-2-regex)!

Use https://regexr.com/ to learn about and test regular expressions!
{% endhint %}


**Table of Contents:**
- [Slides](#slides)
- [Intro](#intro)
  - [What is a Regular Expression](#what-is-a-regular-expression)
  - [What can you do with regular expressions?](#what-can-you-do-with-regular-expressions)
    - [Validate Strings](#validate-strings)
    - [Extract Strings From Text](#extract-strings-from-text)
- [Regular Expression Syntax](#regular-expression-syntax)
  - [There are a lot of cool ways to use regular expressions](#there-are-a-lot-of-cool-ways-to-use-regular-expressions)
  - [Special Characters](#special-characters)
- [How to Regular Expressions in JavaScript](#how-to-regular-expressions-in-javascript)
  - [`RegExp.test(str)` and the `i` flag](#regexpteststr-and-the-i-flag)
  - [`str.search(RegExp)`](#strsearchregexp)
  - [`str.match(RegExp)` and the `g` flag](#strmatchregexp-and-the-g-flag)
  - [`str.replace(RegExp, replacement)`](#strreplaceregexp-replacement)


## Slides

{% embed url="https://docs.google.com/presentation/d/1KNxfevZGXU2srFOejf6qBgOwXznH8ddQ3ePeqTXbGiQ/embed?start=false&loop=false&delayms=3000" %}

## Intro

Consider this function:

{% code title="0-intro.js" lineNumbers="true" %}
```javascript
const isOnlyAlphaNumeric = (str) => {
  if (!str.length) return false;
  const valid = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_'; 
  for (let i = 0; i < str.length; i++) { 
    if (!valid.includes(str[i])) { 
      return false; 
    } 
  }
  return true; 
};

console.log(isOnlyAlphaNumeric('Hello world!'));  // false
console.log(isOnlyAlphaNumeric('Hello_world'));   // true
```
{% endcode %}

Now, consider the function with regular expressions.

### What is a Regular Expression

> A **regular expression** (or "Reg Ex") is a sequence of characters that specifies a "match pattern" to search for strings in text, extract information, or validate input.

To create a regular expression, we use a pair of forward slashes with the match pattern inside. 

```js
const regex = /^\w+$/ 
console.log(typeof regex); // object
```

The symbols and characters inside of the `/ /` define the characteristics of the strings that the regular expression can be used to search for. In the example above, we have:
- `^` requires that the matching string must start with...
- `\w+` one or more word characters (letters, numbers, or `_`). 
  - The `\w` represents a word character and the `+` modifier means "one or more"
- `$` requires that the matching string must end before any other characters are included (no spaces, no symbols, nothing!)

### What can you do with regular expressions?

#### Validate Strings

Like the `isOnlyAlphaNumeric` function, regular expressions are often used to validate if a string matches a specific pattern.

One of the most common challenges in applications is validating proper date formats. This function uses a regular expression to ensure that a given string is in the "MM-DD-YYYY" format:

```js
const isValidDateStr = (dateStr) => {
  const dateRegExp = /^([01]{2}|12)-([0-2][0-9]|30|31)-[0-9]{4}$/;
  return dateRegExp.test(dateStr);
}
```

#### Extract Strings From Text

One cool usage of regular expressions is to "find all instances of X in a string". For example, in a big block of text, grab all of the emails:

```js
const extractEmails = (text) => {
  const emailRegExp = /[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}/g;
  return text.match(emailRegExp);
}

const message = `
Dear Team,

Thank you for attending the meeting earlier today. As discussed, please reach out to the following team members if you have any questions or need further clarification:

For project management updates, contact Sarah at sarah.jones@example.com.
For technical issues, reach out to Mike at mike.smith@example.com.
For client communications, please email Julia at julia.roberts@example.com.
Let me know if you need anything else.

Best regards,
John
john.doe@example.com
`

const emails = extractEmails(message);
console.log(emails);

/* 
[
  'sarah.jones@example.com',
  'mike.smith@example.com',
  'julia.roberts@example.com',
  'john.doe@example.com'
]
*/
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
 
```js
const phrase = 'My cat named caterpillar loves catnip.'
const firstMatch = phrase.match(/cat/);
console.log(firstMatch);
/* 
[
  'cat',
  index: 3,
  input: 'My cat named caterpillar loves catnip.',
  groups: undefined
]
*/

// Without the global flag, match will only return the first match
console.log(firstMatch.length); // Prints 1
console.log(firstMatch[0]);     // Prints 'cat'
console.log(firstMatch.index);  // Prints 3
```

The returned array is a little bit strange looking at first but its just an array with one value, and then the properties `index`, `input`, and `groups`

By default, regular expressions will stop searching after the first match is found. Adding the global `g` flag after the second `/` causes `match` to return ALL matches.

```js
// With the global flag:
const allMatches = phrase.match(/ow/g);
console.log(allMatches);        // Prints ['ow', 'ow', 'ow', 'ow'];
console.log(allMatches.length); // Prints 4
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

