# 20230116
Did most of day 5 of Advent of Code 2022.
Just skipped the parsing of the stacks and manually transformed the input to be easier to read into data structures.

Turned
```
    [D]    
[N] [C]    
[Z] [M] [P]
 1   2   3 
 ```
into
```
ZN
MCD
P
```

The first seemed like a pain to write a parser for.

**Update**
Updated it to handle the parsing.
It's a bit jank and there's probably a nicer way to read it in if I treat it as a matrix but it's not that interesting.

## Finding Integers in Strings
Interestingly `std::basic_string::find` when passed an `int` compiles but gives unexpected behavior.
The `int` needs to be passed into `std::to_string` first.

# 20230115
## Advent of Code - Finding Overlapping Tasks
Did day 4 of Advent of Code 2022 which was a pretty clever logic puzzle.
Most of the fun was writing the boolean expression to check for.
The rest of the string parsing in C++ was rote.
It was good practice for iterators though and I also learned about `std::string::substr`.

I wanted to do this problem with fancier range APIs but the GCC version (9.2.0) that comes with Ubuntu 20.04 is simply too old and it will take a lot of yak shaving right now to get it working.
I would like to revisit this when I have a newer version of GCC.

## String Views
Tried to redo the problem above with ranges but got stuck with converting the result of `std::views::split` into an `int`.
Did some background reading and a `view` is a pointer to the beginning and a pointer to the end.
The advantage of `string_view` over `string` is that it does no allocation.

Still stuck but I found the string_view demo on the C++ Weekly podcast interesting.
The compiler is better able to optimize with a string_view.

# 20130114
Did day 3 of Advent of Code 2022 which was also pretty straightforward.
Learned a bit about converting chars to ints and how `static_cast<int>('A') == 65` while `static_cast<int>('a') == 97`.

In the rush to do the second part, I had a really bad bug when I was filling my sets with the string's contents.
```cpp
string s;
set<char> char_set;
for (int i = 0; i < char_set.size(); i++) {
  char_set.emplace(s.at(i));
}
```
Adding logging statements to print out the set contents after the loop made the mistake obvious. I was looking at `char_set.size()` instead of `s.size()`.

Overall, this one went pretty smooth.
There's probably a nicer way to write the 3-way set intersection I did for part 2 that I can learn about.
There is also probably a more modern way to fill a set from a string, maybe it can be done without an explicit loop nowadays.

# 20230113
## Advent of Code - Rock Paper Scissors Scoring
Did day 2 of Advent of Code 2022.
Was also pretty straightforward like yesterday's.

Learned something new about how to use a `const std::map`.
Interestingly, `std::map::operator[]` is non-`const`, but `std::map::at` is `const`.
`operator[]` can modify the map for writing new values (if the key doesn't exist) or for overwriting (if it does), while `at` is purely for access and throws `std::out_of_range` for non-existent keys.

One interesting improvement would be using a compile-time map that we can make `constexpr`.
The standard library's `std::map` can't be declared `constexpr`.

Could also clean up the string handling a bit and do some string-splitting instead of relying on `istringstream`.
It never feels right to use a string stream.

## LeetCode - First Bad Version
Started looking at LeetCode again and am stuck on #278.
It's a weird variation of binary search where you need to return the first occurence of an element.


**Update**

Got it after working through a couple of examples on paper.
It basically was binary search except I had to refine the search interval for the first occurence.

Getting the termination condition correct was also pretty tricky but some print statements helped a lot.
I wasn't moving the `high` pointer correctly, I mistakenly was moving it to `curr - 1` when given a bad version instead of `curr`.
This broke the loop invariant that the first bad version is always in the interval `[low, high]` since my `high` could get too low.
Did a bit of staring at it and brute forcing special conditions to try and get around it.
It ended up being a distraction.
What did end up working was printing my variables on every loop iteration.

## C++ - Checking for Key Existence in `std::map`
Trying to find a nice C++ equivalent for checking if a key exists in a `std::map`.
C++20 has `std::map::contains` but getting that to compile has been problematic.

# 20230112
Did day 1 of Advent of Code 2022.
It was pretty straightforward once I learned how to do I/O correctly.
Could possibly optimize the second part to only store the top three using a bounded priority queue or other similar data structure.


# 20230111
Needed to read up a bit more on how to read input in C++ to do the first problem of AoC2022.
The simple way of doing `ifs >> i` to process ints doesn't work well with blank lines.
Instead had to read up on how `getline` works and made an example on it [here](https://github.com/jamcag/cppex/blob/4502b8b3f8c43fc4437fc9dbd0073b65ef2b0336/getline.cpp).
