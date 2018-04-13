# Predictive writting

Let go back in time, before smartphones, when we had to sent SMS with 9 buttons.

Multiples algorithms were used in order to make you type fast. In this exercise,
you'll implement multiple algorithms and compare them.

This is how a key pad looked like:

```
-------------------
|  1  |  2  |  3  |
|     | ABC | DEF |
| --- | --- | --- |
|  4  |  5  |  6  |
| GHI | JKL | MNO |
| --- | --- | --- |
|  7  |  8  |  9  |
|PQRS | TUV | WXYZ|
| --- | --- | --- |
|     |  0  |     |
|     |space|     |
-------------------
```

We only handle letters and spaces to make it simpler. We don't do numbers and
we will be case-insensitive. Also we'll consider only English as our target
language.

## Level 1 - Multi-tap

The multi-tap method consists of typing on the same key multiple times in order
to get the letter you want. For instance, to get the letter B you'll need to
press the key 2 twice. For "THE HACKING PROJECT", you'll need 35 taps.

Write a first program that converts a sequence of taps to the resulting word.

```
$ echo "3 332287777" | ruby level1/multi_tap_to_word.rb
debts
```

Spaces in the input means that we needed to wait as the next character is using
the same key as the previous one.

Now write a program that does the opposite:

```
$ echo "debts" | ruby level1/word_to_multi_tap.rb
3 332287777
```

## Level 2 - T9 1/2

[T9](https://en.wikipedia.org/wiki/T9_(predictive_text)) is a dictionary backed
way of typing faster. Compared to multi-tap, you only need to type once on each
key you need then sort out the disambiguation by pressing the _Next_ key if
needed.

For instance, to type _« the »_ we need to press 8, then 4, then 3. Each
sequence of number is mapped to a list of words: `864` could be `the` but it
could also be `tie`. To switch between `the` and `tie`, we need to press the
_Next_ key. We'll use `1` as our next key.

For this level, you'll write the first piece of a T9 engine. Your program must
be able to take a sequence of numbers from 1 to 9 and return the matching word.

```
$ echo "843" | ruby level2/t9_to_word.rb
the
$ echo "8431" | ruby level2/t9_to_word.rb
tid
$ echo "84311" | ruby level2/t9_to_word.rb
tie
$ echo "843111" | ruby level2/t9_to_word.rb
vid
$ echo "8431111" | ruby level2/t9_to_word.rb
vie
```

Once `t9_to_word.rb` is done, write `word_to_t9.rb` that does the reverse.

```
$ echo "tid" | ruby level2/word_to_t9.rb
8431
```

* Get the list of english words from [here](http://norvig.com/ngrams/word.list).

_What do you think about the memory comsumption of you program?_

## Level 3 - T9 2/2

To avoid unecessary usage of the _Next_ key, we would like to put the most
common words first. Update your previous program to take that in consideration.

```
$ echo "8431" | ruby level3/t9_to_word.rb
tie
$ echo "tie" | ruby level3/word_to_t9.rb
8431
```

* Get the sorted list of the most used words [here](http://norvig.com/ngrams/count_1w.txt)

You'll notice that the words in the most used words aren't all in the previous
list. Don't hesistate to merge the lists together.

## Level 4 - Benchmarking 1/2

It is clearly faster to type with T9 rather than with multi-tap. What about the
programs you wrote?

In this level, we would like to see how fast your programs from level 1 and from
level 3 are going. To do that we're gonna take he 1000 most commons words and
see how fast your programs can convert it to key taps then convert it back to
the same word.

Use the `benchmark` ruby library to do that, a template is ready to help you.

```
$ ruby level4/benchmark.rb
                   user     system      total        real
multi-tap      0.010000   0.000000   0.010000 (  0.007173)
t9             5.150000   0.050000   5.200000 (  5.207333)
```

You may find T9 very slow, in that case, you'll need to tune it to get better
performances. Don't hesitate to go back to the previous levels if needed.

## Level 5 - LetterWise

[LetterWise](https://en.wikipedia.org/wiki/LetterWise) is a predictive method
aiming to help you type better on those keypads. The idea, as you can read on
the Wikipedia page is:

> Hit the key with the letter you want,
> if it doesn't come up, hit Next until it does.

With T9, we were waiting to have the whole word typed to being able to do the
disambiguation:

```
Keystroke Display Comment
   4        i     wrong first letter
   6        in    still wrong
   5        ink   still wrong
   3        hold  wrong word appears
   1        hole  adjust - wrong word
   1        gold  adjust - wrong word
   1        golf  adjust - correct word
```

LetterWise has another approach as it makes you do the disambiguation while you
are typing. You always have the right word in front of you because when a letter
is wrong, you need to tap on _Next_ to fix it.

In this level, you'll the create a program that takes a prefix and a key between
2 and 9 then returns a list of possible letters.

```
$ ruby level5/letter_wise_rb 3 th
e d f
$ ruby level5/letter_wise_rb 5
l j k
```

All letters matching the key must be returned. Only the order of those letters
will vary, depending on the prefix. As in the second example, the prefix could
be omitted.

## Level 6 - Benchmarking 2/2

To add you're LetterWise engine to the benchmark, you'll need to do the same
components as for multi-tap and T9: converting a word into a sequence of taps
then converting a sequence of taps back into a word.



-----

**Notes**

You don't have to do all the levels, go as far as you can in a decent amount of
time. The whole test should take a full day of work for a developer with a bit
of experience. If it isn't the case let me know.

All examples aren't necessarly accurate. Correctness isn't that much of an issue
in the whole test as many things depends on your own implementations.

If you precompute anything, be sure to let us know how you did it by including
the code and details about how to use it.

More information on LetterWise can be found in the article:
[LetterWise: Prefix-based Disambiguation for Mobile Text Input](http://www.yorku.ca/mack/uist01.html).
You don't have to read it to complete this level, that more of an FYI.

If you finished the test you basically reverse-enginered a patented technology!
