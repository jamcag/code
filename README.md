# 20230127
Poor sleep and didn't get to do much coding today apart from reading input for AoC '22 day 14.
Tried to do the problem in a fancy way with `std::views::pairwise` but thought I needed to do it on a specific machine with a recent enough compiler so took a long break.
Turns out that even my box with Ubuntu 22.04 doesn't have a g++/libstdc++ that's recent enough to support it.

Could have ran a newer one in Docker but right now I have an aversion to doing sysadmin stuff like that so if I see a language feature I don't see supported again, I'll just solve the problem with whatever is actually available.

Halfway through last year's Advent of Code though which is I think the furthest I've ever gotten!
Hopefully I can finish this one up!

Momentum is a thing so let's try to get the cadence back to daily solutions.

# 20230126
## Advent of Code 2022 Day 13 - Distress Signal
Did day 13.
It was a good progression from part 1 to part 2 but damn that was annoying to read in the input in C++.

I ended up asking ChatGPT for the input reader, and the code it gave didn't even work but it did help me thing of a nice way to parse things like `[1, [2], [[3]], 10]` by incrementally building up digits and then calling `std::stoi` on them.
It alternatively suggests using a JSON library which would have worked nicely too but I'm trying to stay dependency free for these.

I worked with `std::any` a lot for this one and I didn't like how it ended up looking.
Lots of ugly `try`/`catch` blocks and weird operator overloading.

Also ran into a problem calling `std::find` inside my `std::vector<std::any>` for the divider packets. I tried defining my own `operator==(const std::any&, const std::any&)` but the compiler complained.

Anyways, glad to have got that out of the way. I liked how the first part was basically verifying your comparator function which you would then use to sort for the second part.

It should be interesting to see others' C++ solutions for this cause boy mine is UGLY.

## Terminal Navigation in VSCode
Shout out to Ctrl+Up and Ctrl+Down in VSCode's terminal.
It makes it way nicer to find compilation errors.
I used to scroll which was time consuming, or search for my terminal's "enter a command" marker which was tedious especially when I don't have an easy to type ASCII magic marker set up on whatever shell I'm using.

## Aside: Lack of Placeholder Variable in C++
Also had to use `std::tuple` today to return multiple values and it's not as nice as in Python because of the lack of something like Python's convention to use `_` for throwaway placeholders.
While it does kind of work in C++, static typing and const make it not as nice to use since you need to stack multiple underscores when the type changes or if a previously defined one is `const`.

## Multithreading Hello World
Read the first chapter of C++ Concurrency in Action and it ended with a [cool example](https://github.com/jamcag/cppex/blob/main/thread_hello.cpp) of how to use C++ threads.
Would be nice to go through the book but I'm not sure I have a problem that will motivate reading it at the moment.

# 20230125
## Advent of Code 2022 - Hill Climbing
Finally solved AoC '22 day 12 part 1.
The problems I had were:
- movement did not allow for arbitrarily high descents (These elves need climbing gear to climb two levels but apparently don't take fall damage falling from massive heights.)
- the cost function I was using in the priority queue was non-admissible
  - solved this by doing standard A* with Manhattan distance
- my "visited" set accounted for paths and not cells
  - it was very peculiar why it was looping for so long with so few grid cells
  - on initial implementation, I was worried that a cell $c$ might get expanded early and the $S-c$ path would be suboptimal
  - by using the standard "current length + cost-to-go" heuristic for A*, the $S-c$ path is guaranteed to be optimal.

Also did the quick (to-implement) solution for part 2 instead of the faster (to-run).

Also saw some weirdness where I could enqueue a `char` into a `std::set<std::array<int, 2>>`.
Also, there's possibly an off-by-one in my part 2 solution, or I just got lucky.

Anyways, glad to have got that out of the way.

I learned a lot about priority queuing
- The default `std::priority_queue` behaviour is a min-queue using the `std::less<T>` comparator
- That can be reversed by passing `std::greater<T>` as a comparator
- A custom comparator can be defined as `[](T lhs, T rhs) -> bool`
- To make a priority queue with a custom data type, first make a comparison function (e.g. `cmp`) then do `std::priority_queue<T, std::vector<T>, decltype(cmp) pq(cmp)`

Also got some more exposure to other standard library things
- `std::min` is a binary operation
- To find the minimum of a sequence use `std::min_element`
  - This returns an iterator that must be dereferenced to get the actual minimum value
- Using type aliases when I started nesting a bunch of containers was really helpful
  - At one point I think I wrote out `std::priority_queue<std::vector<std::array<int, 2>>, std::vector<std::vector<std::array<int, 2>>>, decltype(l1_dist)>` as type...

## `tviz` - Text Visualizer
Pulled the app out from my imgui fork into its own [separate repo](https://github.com/jamcag/tviz).
Not sure if I will ever pick it up again but ImGui seems pretty handy for quick GUIs.
I'm still not sure how it interacts with the different backends and which would be best though.
Maybe I can just skip ImGui altogether and use bare GLFW (or one of those other backends it supports).

Not the most interesting project so no concrete plans for this one.

# 20230124
I picked AoC '22 day 12 back up again but still haven't solved it.
Tried
- Updating the priority queue function to account for Manhattan distance to the `E`
  - It expanded fewer nodes on the example input but was still getting stuck at the end.
- Changing the "visited" set to track cells instead of paths
  - Also lead to faster chugging through small input but still got stuck.


The failure case is when it gets stuck in a big valley of flatness.

```
cccccccccccccccccccccccccccccccccccccc
SbcccccccccccccdefghijklmnopqrstuvwxyE
cccccccccccccccccccccccccccccccccccccc
```

Here, once it reaches a 'c', it starts expanding a bunch.

## Hill Climbing Visualization Aside
Went down a couple rabbit holes trying to visualize my pathfinding state.
Had a couple of false starts with ImGui and React.
ImGui is really verbose while React would mean translating my solution into JS/TS.

Ideally since I already am outputting a visualization to the terminal, there should be a library that lets you clear and update text in place instead of dumping extra lines.
The most popular library for this is ncurses but it's API is CLUNKY.

It got me thinking about a simple app that you could paste formatted text into and would let you play it back.
Would be nice.

**Update:** Made the a quick and dirty [text frame app](https://github.com/jamcag/tviz).
It takes newline separated frames in a file called `input` and it lets you scroll through the frames.
The round trip of saving the text output, updating the input file, and relaunching the app is pretty involved though so I'm not sure how much mileage this will get.

Features that would be nice:
- Stream output from a running program
- Play the text automatically

**Update 2 (20230125):** Opening the file up in vim and holding `]` achieves most of what I want.
The FPS is pretty low but is enough for intuition.

# 20230123
I gave day 12 of AoC '22 a solid shot but am stumped.
It was super easy to get it working with the example input but the search tree is too big when doing it on the real input.

Tried crafting a couple of heuristics to do A* with instead of BFS but it is still getting stuck.

I might also have bugs somewhere but it is now very late.

Disappointed that my streak of Advent of Code solving is ending.
Did way too much today anticipating that this would be easy after glancing at the problem.
Also had very short sleep the last night.

Oh well.

# 20230122
Did part 1 of AoC '22 day 11 relatively quickly.
Part 2 is tricky with dealing with integer overflow though.
I tried upping my `int`s to `int64_t` and even `__int128` which gcc supports but was still getting overflow.
My next idea is to implement my own integer class with addition, multiplication, and modulo operators.

**Update:** While perusing JavaScript's docs on their `BigInt` values I got the idea to see if using `double`s would work and it did up to 20 rounds.
I started to get `inf`s when I upped the number of rounds to 1000 though.
Even at 1000 rounds, I was getting numbers with 100-200 digits.
I'm unsure if implementing big integers myself will work when the number of digits get even higher at 10000 rounds.

Trying out the GMP library.
Interestingly, I need to compile with `-l` flags at the end.

```
g++ -g gorilla_business.cpp -o gorilla_business -lgmp -lgmpxx
```
works but
```
g++ -g -lgmp -lgmpxx gorilla_business.cpp -o gorilla_business
```
doesn't.

**Update 2:** GMP implements arbitrarily big integers in C++ with the `mpz_class` data type.
Doing the quick and fast replacements to use this type is giving me a segfault when it calls the lambda.

**Update 3:** Removing the lambda and replacing it with `operator()` instead got around the segfault.
It is now slowing to a crawl however when it reaches ~round 1000.

The fact that it's taking at least five minutes and is still in the 1000s suggests there's a more clever way of solving this one.
Maybe there's some way to truncate the numbers in a smart way that allows us to pass it through operations while preserving divisilibty checks.

**Update 4:** Had to read a [hint on reddit](https://www.reddit.com/r/adventofcode/comments/zih7gf/2022_day_11_part_2_what_does_it_mean_find_another/).
Pretty disappointed that I couldn't figure out myself.
Not sure I quite understand why it works either, though my gut was telling me it had something to do with this.

I previously tried truncating to $n$ digits (for various values of $n$) but would be off.
Intuitively, checking the last couple digits for divisibilty should be enough and I don't think it should affect addition and multiplication for those last few digits but it does.

I tried working it for about two hours (across maybe 12 hours on and off).
Maybe I could have eventually figured it out myself but it is getting late and I wanted to move on.
I could have just left it unsolved but it would have bothered me.
I think I gave it a solid effort trying to use a modulo trick,  then trying to implement big integers, then integrating GMP to see it fail but I just didn't know enough number theory.

I have a feeling the underlying number theory will be nagging at me now but I'll deal with it.

# 20230121
**Laterblog (from 20230122):** Yesterday, I did AoC '22 day 10 part 1 pretty fast then procrastinated on the second part since I thought it would be hard.
Once I sat down to do it and full sent the coding, it was really quick to do though.

I enjoyed the ASCII art.

# 20230120
Very wordy description for AOC '22 day 9.

**Update:** Now done.
That was pretty fun.

Learned about `std::vector::back` recently which has replaced my usage of `vec[vec.size() - 1]`.
Way nicer.

Also made a [`std::accumulate` example of how to take the row-wise sum, column-wise sum, and total sum of a 2d vector](https://github.com/jamcag/cppex/blob/fcc92b0682a8be9657c9d014b3ba53db50d17d9e/accumulate.cpp#L21-L45).
The column-wise one isn't super nice.
I'm hoping to find a better way of writing it.
I see there's some utilities to flatten an array into views and maybe there's some way to make the iterator skip across elements the correct way to go down a column.

# 20230119
## Advent of Code 2022 - Tree House
Day 8 seems fun with a bit of numerical thinking.

**Update:** It wasn't all that numerical but got a little caught up trying to think of the boolean conditions for the first problem and the tree counting for the second problem.
For the first problem, it took a while to write the visibility condition.
Had to iterate on small grids to triple check my logic and walk through examples in the code comments.
The second problem was supposed to be straightforward using the first as starter code but I wasn't incrementing right.

The way I wrote my loops made it very error-prone.
Writing this in a more functional way without loop variables would improve readability and modification.
It was pretty dicey updating each loop for each search direction.
I've definitely wrote code like this before and wanted to improve it but I haven't went back to them.
This should be a good example to dip my feet into functional C++.

## Another Solution to AOC'22 Day 1
Inspired by a day of cooking, I'm deciding look at other people's solutions after I do problems starting with one for the Calorie Counting problem.
Story is that I didn't code at all after solving the problem and spent most of my free time today learning to cook a new dish.
After I finished, I ended up watching other variations of the recipe and learned a lot from seeing different variations.
There's YouTube videos of people presenting their solutions and I found a good one from Kjell.

### Staged Processing
Kjell's solution processed data in stages.
I opted to avoid storing intermediate data structures and did the work as I was reading from standard input.
This gave a pretty simple solution for a pretty simple problem.
Kjell decomposed his into stages:

1. Read data into `vector<string>` of lines.
2. Process into a `vector<vector<int>>` of the calories for each elf.
3. Use `std::accumulate` to sum up calories for each elf and `std::max_element` to find the maximum.

I had thought of doing it in stages too but it seemed overkill for this problem.
I think it would be good as an exercise to do it this way too though.

**Aside:** My yak shaving tendencies made me start thinking of the best way to present different solutions in a git repository.
The options are (1) side-by-side files, (2) different branch for different solution, (3) just simply overwriting the original file on `main`.
While I think (1) and (2) have benefits, I think it is definitely overkill and the benefit would be marginal.
I'll just code and push.

### Structural Differences
I found their code structure interesting.
They had one file for both problems and his executable would run on both the example inputs and real inputs for both days outputting in the form
```
[Part 1 Example]: ...
[Part 1 Real]: ...
[Part 2 Example]: ...
[Part 2 Real]: ...
```
Pretty spiffy in my opinion but it also leads to bigger files but avoids needing to copy paste duplication.
The classic trade-offs between making a library function vs. copy-pasting likely also apply.

Additionally, I noticed they also used a different namespace for the "runners" for the different days.
It roughly looked like
```cpp
namespace part1 {
  void run(char* input_file) {...}
}

namespace part2 {
  void run(char* input_file) {...}
}
```

Their main function then looked like
```cpp
int main() {
  part1::run("example");
  part1::run("test");
  part2::run("example");
  part2::run("input");
}
```
This template seems pretty handy for someone trying to do the problems as fast as possible for the leaderboards.
Definitely made me pause and think what optimizations do my workflow I could do.
Optimizations that would actually save me time that is.

**Conclusion:** Overall, I learned a lot seeing what someone else produced.
His presentation walked through the code.
It will be interesting to see someone live coding their solution to see their thought process.
It should be just like interviewing someone.

# 20230118
## Advent of Code - Freeing Device Space
Just read the day 7 problem for Advent of Code 2022 and it's now ramping up in difficulty.

**Update:** Finally finished.
It wasn't hard but just more complex than past problems.
Not happy with my code but I spent at least 5 hours on this already.
Not 5 hours continuously.
I procrastinated a lot when I got blocked trying to implement it all at once.

Made progress faster when I started with minimal examples and incrementally added more features.
Helped to move away from my surroundings and restart in a different room somewhere more comfortable.

## First Render
Left a render running last night of the detections on my video and I'm pretty happy with it.
It's interesting to see how it loses track of me sometimes and how it's identifying holds as objects.
Judging by the rapidly changing of border colors it appears that it flickers a lot between different object class predictions.

# 20230117
## Advent of Code - Signal Detection
Did day 6 of Advent of Code 2022.
Easiest one yet.

Could clean it up and optimize further.
Probably is a way to make the loops read nicer.
I could also do a rolling update of the set of last four characters or keep track in a better way.
It would only improve runtime by a constant factor though so not super keen on working on this further.

## `bboxer` - Object Detector CLI
Wrote CLI tool [bboxer](https://github.com/jamcag/bboxer) to run a pre-trained FasterRCNN over images and draw the predicted bounding boxes. It runs pretty slow and doesn't seem to be using the GPU from what I can tell in `nvidia-smi`.

This is the first step in writing a video autocropper that I want to make. On top of this, I can crop videos to the detections and start working on tracking.

Synthesized this tool from some disparate PyTorch documentation. Their [tutorial](https://pytorch.org/tutorials/intermediate/torchvision_tutorial.html) on finetuning and adding a head to a pre-trained detector was very useful for instantiating and using a detector. I then built on top of it with bounding box drawing and image saving. Tied it altogether by packaging it all into a command line tool.

**Thought:** The images being 4K might also explain why it's going so slow. It might be worth giving it a shot to downscale the images and see how the performance is.

## Trying Out `pytracking`
Found the [`pytracking`](https://github.com/visionml/pytracking) library on Papers with Code and it seemed interesting and I wanted to try it out.
Got stuck setting it up though.
One of its dependencies `spatial-correlation-sampler` needs `CUDA_HOME` to be set but it's not obvious to me where that should be with my setup.
I'd expect it to be in `/usr/local/cuda` or in `/opt` it's not there.
Searching around for this info wasn't very helpful either.

For future reference, I installed CUDA 12.0 using the official docs but also have CUDA 11.1 from the PyTorch instructions and CUDA 10.0 from the pytracking instructions.

**Update:**  The `spatial-correlation-sampler` dependency is only needed for the tracker unfortunately named KYS.
I just won't use that one.

### CUDA on Ubuntu Weirdness
 For whatever reason, to run `nvidia-smi` I had to install `nvidia-utils-525` which uninstalls  the system `nvcc` given by `nvidia-cuda-toolkit`. Maybe they conflict in some way and uninstall each other?
```
$ sudo apt install nvidia-utils-525
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  javascript-common libaccinj64-11.5 libcub-dev libcublas11 libcublaslt11 libcudart11.0 libcufft10 libcufftw10 libcupti-dev libcupti-doc libcupti11.5 libcurand10 libcusolver11 libcusolvermg11
  libcusparse11 libegl-dev libgl-dev libgl1-mesa-dev libgles-dev libgles1 libglvnd-core-dev libglvnd-dev libglx-dev libjs-jquery libnppc11 libnppial11 libnppicc11 libnppidei11 libnppif11 libnppig11
  libnppim11 libnppist11 libnppisu11 libnppitc11 libnpps11 libnvblas11 libnvjpeg11 libnvrtc-builtins11.5 libnvrtc11.2 libnvtoolsext1 libnvvm4 libopengl-dev libpthread-stubs0-dev libtbb-dev libtbb12
  libtbbmalloc2 libthrust-dev libvdpau-dev libx11-dev libxau-dev libxcb1-dev libxdmcp-dev node-html5shiv nvidia-cuda-gdb nvidia-cuda-toolkit-doc nvidia-opencl-dev ocl-icd-opencl-dev opencl-c-headers
  opencl-clhpp-headers x11proto-dev xorg-sgml-doctools xtrans-dev
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  libnvidia-compute-525
Suggested packages:
  nvidia-driver-525
The following packages will be REMOVED:
  libcuinj64-11.5 libnvidia-compute-495 libnvidia-compute-510 libnvidia-ml-dev nvidia-cuda-dev nvidia-cuda-toolkit nvidia-profiler nvidia-visual-profiler
The following NEW packages will be installed:
  libnvidia-compute-525 nvidia-utils-525
0 upgraded, 2 newly installed, 8 to remove and 125 not upgraded.
Need to get 0 B/50.6 MB of archives.
After this operation, 2,178 MB disk space will be freed.
Do you want to continue? [Y/n] y
Get:1 file:/var/cuda-repo-ubuntu2204-12-0-local  libnvidia-compute-525 525.60.13-0ubuntu1 [50.2 MB]
Get:2 file:/var/cuda-repo-ubuntu2204-12-0-local  nvidia-utils-525 525.60.13-0ubuntu1 [356 kB]
(Reading database ... 216436 files and directories currently installed.)
Removing nvidia-cuda-toolkit (11.5.1-1ubuntu1) ...
Removing nvidia-cuda-dev:amd64 (11.5.1-1ubuntu1) ...
Removing nvidia-visual-profiler (11.5.114~11.5.1-1ubuntu1) ...
Removing nvidia-profiler (11.5.114~11.5.1-1ubuntu1) ...
Removing libcuinj64-11.5:amd64 (11.5.114~11.5.1-1ubuntu1) ...
Removing libnvidia-ml-dev:amd64 (11.5.50~11.5.1-1ubuntu1) ...
Removing libnvidia-compute-495:amd64 (510.108.03-0ubuntu0.22.04.1) ...
Removing libnvidia-compute-510:amd64 (510.108.03-0ubuntu0.22.04.1) ...
Selecting previously unselected package libnvidia-compute-525:amd64.
(Reading database ... 214315 files and directories currently installed.)
Preparing to unpack .../libnvidia-compute-525_525.60.13-0ubuntu1_amd64.deb ...
Unpacking libnvidia-compute-525:amd64 (525.60.13-0ubuntu1) ...
Selecting previously unselected package nvidia-utils-525.
Preparing to unpack .../nvidia-utils-525_525.60.13-0ubuntu1_amd64.deb ...
Unpacking nvidia-utils-525 (525.60.13-0ubuntu1) ...
Setting up libnvidia-compute-525:amd64 (525.60.13-0ubuntu1) ...
Setting up nvidia-utils-525 (525.60.13-0ubuntu1) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
```
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
Updated it to handle the parsing so now I'm done all of day 5 of Advent of Code 2022.
It's a bit jank and there's probably a nicer way to read it in if I treat it as a matrix but it's not that interesting.

## Finding Integers in Strings
Interestingly `std::basic_string::find` when passed an `int` compiles but gives unexpected behavior.
The `int` needs to be passed into `std::to_string` first.

## LeetCode - Ransom Note
Did the Ransom Note problem  on LeetCode, here's my [solution](https://github.com/jamcag/cpplc/blob/main/ransom_note.cpp).
First did it using `std::map::contains`, but LeetCode's compiler doesn't support it yet.
One C++17 way to do it is to use `std::map::count(K key) -> int` which returns 1 if a key exists and 0 if it doesn't.

Lots of room for improvement with our implementation at 40th percentile for runtime and 20th percentile for memory usage.
This was expected since I just wanted to move on after wrangling with compiler issues instead of optimizing.

We know some obvious optimizations though to improve both runtime and memory.

We loop through `magazine` which we shouldn't need to.
Lazily reading through `magazine` until we find a character of interest in `ransom_note` will improve the runtime if we find the character of interest early.

~~Our ranking on memory is bad since store every character of `magazine` in a `map`.
This is totally unnecessary but was done for convenience.
By doing the search for characters as we go through `ransom_note` we could totally remove the `map`.~~
**Correction:** This is wrong since if we want to only iterate at most once through `magazine`, we need to store letter counts.


```
char_counts = {}
for each character c in |ransom_note|:
  check char_counts if c is available, continue if it is
  search for c in |magazine|, if we reach the end then return false
  add chars to char_counts as we go through |magazine| if they aren't matches
```

We continue the search through `magazine` from the last position.

This should be the optimal solution which is $O(m + n)$ where $m$ is the length of `ransom_note` and $n$ is the length of `magazine`. This is optimal because we need to go through every character of `ransom_note` to check for them in `magazine`. Worst case, magazine doesn't contain the letter and we need to compare against every character in `magazine`.

### Ransom Note without Map
Without using a map we get 75th-percentile in runtime (still not optimal since we restart the search from the start for each ransom note character) but jump up to 90th-percentile in memory.

## Installing OpenCV from Source
To use Darknet on videos, we need to make OpenCV discoverable by pkg-config. The packaged OpenCV that comes with Ubuntu doesn't seem to do this correctly so we needed to build from source.

Usually we create a .deb installer instead of running `make install` but OpenCV ~~comes with an `uninstall` target which should do any cleanup we need.~~ **Update:** Doesn't seem like there's an `uninstall` target. ~~Also, Darknet needs OpenCV 3 so we needed to reinstall. For future reference, the OpenCV 4 we installed was on `c63d79c5b16fcbbec46f1b8bb871dab2274e2b01`. ~~ 
**Correction:** Actually there is an `uninstall` target.
It doesn't get rid of the `pkg-config` setup though so I can clean that up later if I install OpenCV later.
OpenCV3 didn't work with Darknet either. and I had to apply a patch.

**Sidenote:** On our PC without case fans, this brought our CPU temps to about 74°C.

### Missing
Had to apply a patch for a missing class.

### Linking Failures

After getting Darknet built, I still couldn't run it.
```
jc@jammy:~/src/tp/darknet$ ./darknet
./darknet: error while loading shared libraries: libopencv_highgui.so.407: cannot open shared object file: No such file or directory
```

Had to write `/usr/local/lib/` into `/etc/ld.so.conf.d/opencv.conf` and run `sudo ldconfig -v`.

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
