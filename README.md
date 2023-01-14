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

# 20230112
Did day 1 of Advent of Code 2022.
It was pretty straightforward once I learned how to do I/O correctly.
Could possibly optimize the second part to only store the top three using a bounded priority queue or other similar data structure.


# 20230111
Needed to read up a bit more on how to read input in C++ to do the first problem of AoC2022.
The simple way of doing `ifs >> i` to process ints doesn't work well with blank lines.
Instead had to read up on how `getline` works and made an example on it [here](https://github.com/jamcag/cppex/blob/4502b8b3f8c43fc4437fc9dbd0073b65ef2b0336/getline.cpp).
