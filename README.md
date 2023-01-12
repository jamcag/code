# 20230112
Did day 1 of Advent of Code 2022.
It was pretty straightforward once I learned how to do I/O correctly.
Could possibly optimize the second part to only store the top three using a bounded priority queue or other similar data structure.


# 20230111
Needed to read up a bit more on how to read input in C++ to do the first problem of AoC2022.
The simple way of doing `ifs >> i` to process ints doesn't work well with blank lines.
Instead had to read up on how `getline` works and made an example on it [here](https://github.com/jamcag/cppex/blob/4502b8b3f8c43fc4437fc9dbd0073b65ef2b0336/getline.cpp).
