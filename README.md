# Extract String Numbers

This is a small JS library that effectively pulls all the numbers written in a string out, storing the numbers and leftover bits of string together so they can later be recombined.

An example use case, the one this library was made for, is providing the ability to multiply the quantities of ingredients listed in a recipe.

The module exports three functions, two are the primary ones and the last is a sort of example of the others' usage.

## splitByNumbers(string)

This one's the main event- it uses a regular expression to split the numbers out of a string, uses `numeric-quantity` to turn the extracted number strings into real numbers, then packs the results in an object with the following shape:

```javascript
// input: "1 string 2"
{
  numbers: [1,2],
  strings: ["", " string "]
}
```

You may notice that the `strings` array starts with an empty string: this is both a result of `Array.split` and an intended feature: it means you can reliably always start with `strings` when rebuilding the string, even if the string starts with a number.

## reconstitute({strings, numbers}, vulgar: Boolean)

Where there's a function that splits the string, there obviously needs to be one that recombines it- and here it is!

`reconstitute` builds a string from an object with the `splitByNumbers` output shape, using `format-quantity` to format the numbers in fraction form (i.e. 1.5 => "1 1/2"). If you want to run operations on the numbers, the intended method is to provide `reconstitute` with an object modified with `map` like so:

```javascript
return reconstitute(
  {
    strings,
    numbers: numbers.map(x => x * 2)
  }
);
```

The `vulgar` argument passes through to `format-quantity`, formatting fractions in unicode vulgar style ("1/2" => "½").

## multiplyNumbersInString(string, multiplier: Number, vulgar: Boolean)

This function is a practical application of the previous two: give it a string and a number to multiply by, and it will return a string with all numbers in it multiplied by that number!

Here's the source, as it's arguably just as important as an example of using the other two functions:

```javascript
const multiplyNumbersInString = (str, multiplier, vulgar) => {
  const { strings, numbers } = splitByNumbers(str);
  return reconstitute(
    {
      strings,
      numbers: multiplier ? numbers.map(x => x * multiplier) : numbers
    },
    vulgar
  );
};
```
