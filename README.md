# 20231020

I often find myself wanting to commit quick in cases where I know I'll rely on looking at diffs instead of messages to understand changes so I made a git alias for it.
```
git config --global alias.save 'commit -m "" --allow-empty-message'
```

This has the benefit of not literring your repo with a bunch of garbage commit messages like "askldjaklcxjnzkl" or "." or "fix".
Instead you'll see no commit message.
Wow.

# 20230916
Installed PyTorch on the laptop.

```sh
conda install pytorch::pytorch torchvision torchaudio -c pytorch
```

# 20230825
Learned about type traits today while trying to implement a generic vector dot product.
```cpp
#include <iostream>
#include <type_traits>
using namespace std;

template <typename T>
struct can_dot {
  static constexpr bool value = is_arithmetic_v<T> && !is_same_v<T, char>;
};

template <typename T>
constexpr bool can_dot_v = can_dot<T>::value;

int main() {
  cout << boolalpha;
  cout << "can_dot_v<int>: " << can_dot_v<int> << "\n";
  cout << "can_dot_v<double>: " << can_dot_v<double> << "\n";
  cout << "can_dot_v<float>: " << can_dot_v<float> << "\n";
  cout << "can_dot_v<char>: " << can_dot_v<char> << "\n";
}
```

- `value` is static since it's a property of a type
  - It lets you access the value without constructing an instance of an object 
    - Without it would need `can_dot<int> trait;`, then `trait.value;` to access the result.
- To avoid having to do `can_dot<T>::value`, we define a [variable template](https://en.cppreference.com/w/cpp/language/variable_template) `can_dot_v<T>` as a shorthand
# 20230824
Dipping my toes into parameter pack and a wrapper to `std::tuple` is one of the simplest ways to use them.

```cpp
#include <iostream>
#include <tuple>
using namespace std;

template <typename... Ts>
class Tuple {
public:
  Tuple(tuple<Ts...> data) : data_(data) {}
  Tuple(Ts... data) : data_(data...) {}
  tuple<Ts...> data() { return data_; }
private:
  tuple<Ts...> data_;
};

int main() {
  Tuple<int> t = make_tuple(3);
  cout << get<0>(t.data()) << "\n";
}
```
# 20230823
Trying to learn some more advanced C++ starting with templates.

A very basic template function is an add function that takes in two members of type `T` and returns the result.
```cpp
template <typename T>
T add(T t1, T t2) {
  return t1 + t2;
}
```

You could use it like `add(1, 2)` or `add(1.0, 2.0)` but not like `add(1.0, 2)` because it expects both arguments and the return types to be the same.
GCC 12.1.0 errors when trying to do the call this way.
```
solution.cpp: In function ‘int main()’:
solution.cpp:14:14: error: no matching function for call to ‘add(double, int)’
   14 |   cout << add(1.0, 2) << endl;
      |           ~~~^~~~~~~~
solution.cpp:5:3: note: candidate: ‘template<class T> T add(T, T)’
    5 | T add(T t1, T t2) {
      |   ^~~
solution.cpp:5:3: note:   template argument deduction/substitution failed:
solution.cpp:14:14: note:   deduced conflicting types for parameter ‘T’ (‘double’ and ‘int’)
   14 |   cout << add(1.0, 2) << endl;
      |           ~~~^~~~~~~~
```
# 20230813
## Efficient Heap Queues in Python
The `heapq` module comes with two functions `heapreplace` and `heappushpop` to simultaneously add and remove an item from a heap.
These are faster than two separate calls to `heappush` and `heappop`.

Functionally, `heapreplace` first removes an item and then pops from the heap.
```py
>>> heap = [2]
>>> heapq.heapreplace(heap, 1)
2
```

Functionally, `heappushpop` inserts an item first and then pops from the heap.
```py
>>> heap = [1]
>>> heapq.heappushpop(heap, 2)
1
```

# 20230809
## Diameter of a Tree Revisited
Created the [dead basic implementation](https://github.com/jamcag/diameter/blob/0e027877947d0a170e0e298f6c33af54ab80dbb4/slow_diameter.py) for `diameter` which is pretty clear.
There's some obvious recomputation going on which we might be able to speed up by caching, I think the bottom-up traversal was probably the better solution than this recursive solution but it got complex real quick.
Am only getting 10-th percentile runtime and 40-th percentile memory with this approach.
Should re-visit as a challenge to myself for writing hairy traversal code when I am ready to review.

```py
def diameter(self):
    if not root:
        return 0
    return max(diameter(root.left), diameter(root.right), max_depth(root.left) + max_depth(root.right))
```

## Python `dict` Keys Must Be Immutable
> [...] dictionaries are indexed by keys, which can be any immutable type; strings and numbers can always be keys.

[Source](https://docs.python.org/3/tutorial/datastructures.html#:~:text=dictionaries%20are%20indexed%20by%20keys%2C%20which%20can%20be%20any%20immutable%20type%3B%20strings%20and%20numbers%20can%20always%20be%20keys)

## Maximum Subarray
There are divide-and-conquer, dynamic programming, and a shortest path reduction which can solve this but the simplest is [Kadane's algorithm](https://en.wikipedia.org/wiki/Maximum_subarray_problem#Kadane's_algorithm). I tried for a couple hours on this one already but couldn't come up with it myself. I read a brief description of the algorithm and on step `j` you maintain two variables `current_sum = sum(nums[i:j])` for all `i` and `best_sum = max(nums[i:j])` for all `i` and `j`. Relating the two is not super clear to me but I'll keep working on it.

# 20230807
## Diameter of a Tree
Pretty stumped on this question and feel like I'm getting pretty lost in the complexity.
My implementation after about an hour of working through it is [here](https://github.com/jamcag/diameter/blob/7b166805214a6ec4a16429e771f2613d641029b2/diameter.py).

It's a bit of a mess but conceptually pretty simple.
The diameter either includes the current node or doesn't.
- If it does, then the diameter `max_depth(node.left) + max_depth(node.right)`
- If it doesn't then it's one of the sub-tree diameters.

We compute it bottom up.

**Update** Looked at some descriptions of the solution and I'm on the right track so it's primarily a coding problem from here which should be good practice.

# 20230328
Back on this coding grind and it seems like I've forgotten a lot from not practicing.

Trying to get the first example from The Algorithm Design Manual in an executable state and in trying to create a test input array I've forgotten how to declare and initialize a C-array on the stack.

My feeling is it's
```c
int s[] = {1, 2, 3};
```

**Update:** That was right.

On a side-note, I'm looking for a fast workflow to test out C/C++ snippets and I think CLion's Scratch Files is probably the best available option.

Requirements for me are:
- Can create a file that I can run

# 20230302
Got my C++ version of the lox scanner working.
Couldn't do it exactly the same way where the error reporting lives in the `Lox` class inside a static function like in Java.
Looked at someone else's implementation and they create a separate `ErrorHandler` class and pass it around instead.
The book suggests this as a better way anyways so not gonna get too hung up on not matching the original implementation as closely as I can.

# 20230301
Went through Chapters 1 to 4 of Crafting Interpreters.

# 20230228
Made the Celsius-Fahrenheit converter in Qt 6 from 7GUIs.

# 20230227
Did the [Majority Element](https://leetcode.com/problems/majority-element/) problem on LeetCode.
It was graded Easy.
Pretty bad 10th percentile run-time and 5th-percentile memory usage.
Would be one to revisit.

# 20230224
Also no coding, let's just call it a weekend.

# 20230223
Didn't do any coding today, twas a pretty slow day life wise.

# 20230222
The random walk continues and did some Kotlin today.
Read Kotlin in Action up to "2.1.2 Functions", pick up from "2.1.3 Variables" next.

Also picked up Stroustup 2014 again reading "3.5 Assignment and initialization", continue from "3.6 Composite assignment operators".

Messed around with QFormLayout.
Trying to find a fast way to insert a new executable into a CMake project.
CLion's new file dialogs aren't great since they mostly assume you're creating new C++ files and adding them to existing targets as opposed to creating a new target.
Its C++ scratch file is pretty good since it can automatically run scratch files with minimal fuss.
Downside is that you can't rename the scratches and for some reason it uses `g++` on Windows so my setup with `std_lib_facilities.h` doesn't work.
While a quick Qt app would work okay, it would ideally be integrated in all the ways I work with C++ so: terminal, VSCode, and CLion.

**Update:** Tested it out in Visual Studio 2022 as well and when I try adding a new file it suggests adding it as a dependency to all executables...

It makes sense why it is this way though, for a typical application there is only one executable.
I just wish these tools had more support for multi-executable projects.

Made a the counter app but for Android.
There's an interesting XML/Kotlin split going on in that world.
The tooling support is great so it isn't a big problem but would be interesting to see what a big app is like.

# 20230221
Did a sidebar into Qt today and made a counter app from the 7GUIs benchmark.
Putting benchmark implementations into [jamcag/7guis-qt6](github.com/jamcag/7guis-qt6).

# 20230220
Trying to do the deployment exercise and it seems like the fastai `predict` API for classification has changed.

The last time I ran things on Feb 9, I was able to pass a `PILImage` into `predict` but now it gives an error about a missing `read` attribute.
This made me suspect it wanted a file name instead and passing in the file name instead worked.

This no longer works
```py
learner.predict(PILImage("image.jpg"))
```

Instead you need to do
```py
learner.predict("image.jpg")
```

## Educative - C++ Fundamentals for Professionals
Looking at this class and it needs some heavy proofreading since I'm finding an issue in every section so far.
It's covering some topics I haven't run into before as well as forcing me to critically read every example because of how many errors it has.

Some highlights
- A lot of the algorithms in the `<functional>` header in the STL work in-place (at least the ones they show in the intro like `accumulate`, `remove_if` and `transform`)
- `std::remove_if` returns a past-the-end iterator for the filtered sequence
  - it modifies the sequence in place putting everything removed after the returned iterator
  - I thought it would just remove elements
    - The way it does things has the benefit of not modifying the size of the sequence
    - It's pretty non-obvious though
- The way to implement a user-defined literal is with `operator""_<suffix>`
  - Example: a metre `_m` suffix on double would be `Distance operator""_m(double d)`
  - Example: kilometre `_km` suffix on double would be `Distance operator""_km(double d)`
- `bool` can be initialized to `nullptr` for some reason
- The syntax for pointers to members is incredibly ugly
  - For a class like `struct A { int x; };`
  - You can make a pointer to member like `int A:: * p = &A::x;`
  - Example usage: `A a{3}; std::cout << a.*p << std::endl;` prints 3
  - We thankfully can just use `auto` and do `auto p = &A::x`
- The website [cppinsights.io](cppinsights.io) is a cool tool for desugaring (among other things) the deduced type from `auto`
- To make a `std::string` with `auto` you can use the C++ string literal suffix from `std::string_literals::s`
  - Ex: `"Some string"s`

# 20230219
Watched Lecture 2 of the fastai videos.
Pretty cool deployment stuff.
Save a model as a pickle.
Use HuggingFace Spaces and Gradio to serve a model.
Git-based workflow to deploy.
Gradio doesn't support tensors or numpy arrays so need to convert first.
Streamlit more customizable.

HuggingFace Spaces automatically hosts your Gradio app as a JSON-HTTP API too so can integrate quick with any frontend once deployed.

**Aside:** Also learned a cool thing about GPU memory.
Unlike regular memory, GPU memory doesn't swap and instead crashes with out-of-memory instead.
I've seen it before but never heard it said before.

# 20230218
## Ray Tracing in One Weekend
Finished the Ray Tracing in One Weekend book.
Pretty cool approaches to splitting up rendering code and data representation.
Every shape is a hittable with some material.
The material defines what happens when a ray hits it.
The render time is proportional to the size of the image and the number of objects in the image.
A lot of the math comes from physics with some cool approximations.

## Programming: Principles and Practice in C++
Started reading this classic.
Was apprehensive before since it only covered up to C++14 but it should hold up well since C++{17, 20, 23} aren't as revolutionary as C++11 was.
Putting my code from this book in [jamcag/ppap-cpp](https://github.com/jamcag/ppap-cpp).

### Chapter 1
The first chapter is a motivating chapter on programming which has a couple of gems.
Sea transport is amazingly cheaper than land transport.
Intuitively this makes sense since shipping stuff in North America is pretty expensive but it hits different to read it stated so matter-of-fact.

Also interestingly, the phone system has five 9's of reliability (20 minutes of downtime in 20 years).

Read that chapter up to "1.5.3 Telecommunications", if I pick it up again it will be from "1.5.4 Medicine".

### Chapter 2
Pretty basic "Hello, World!" example.
Not much else interesting.

### Chapter 3
I read up to "3.4 Operations and operators", if I pick it up again it will be from "3.5 Assignment and initialization".

## Advent of Code '22 - Day 24
Read the description but didn't feel like I knew a solution right away so decided to skip.

# 20230217
Didn't get around to coding today.
Was pretty low energy all day.

Read a bit about why GUIs are hard in Rust.
Turns out it has no inheritance.
Wild.

# 20230216
Managed to pass the public test case for Day 22 but couldn't get the answer.

Wrote a visualizer [gridviz](https://github.com/jamcag/gridviz) to try and debug but ran out of time.

Watched a talk about ChatGPT, main parts are autoregressive learning and reinforcement learning from human feedback.
# 20230215
## Advent of Code 22 - Calculator
Did part 1 of AoC '22 Day 21.
Pretty simple.

Then tried to get clever and do part 2 using gradient descent.

## Micrograd
Watched the video on micrograd.
It's pretty informative.
Neat how he spends about an hour doing manual chain rule just to cover two examples.
Reminds me of a university tutorial.

Didn't end up getting to solving the part 2 problem but I think it should work.

Also didn't finish the video, I had 20 minutes left but got tired.
Watching material like this is exhausting in a good way.

# 20230214
## Advent of Code 2022 - Day 20 Fail
Caught up with a lot of BS working on AoC'22 Day 20.
Was jumping around between solution methods a bunch.
Did a lot of false starts from a blank file.
Initially was just gonna do it top-to-bottom in main.
Then thought of implementing some `left` and `right` functions to do the shuffling.
Finally, was gonna do a class to implement it.

Overall, just didn't make any meaningful progress.

## Setting Up Global Catch2
In the process, I did some yak-shaving trying to get my environment set up like CoderPad so I could just plop some quick unit tests into any C++ file.
I primarily was interested in being able to stick
```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
```
at the top of any cpp file and have it work but something happened with Catch2 that made it really slow to compile.

With a normal main function it takes 0.5 s while with Catch2 v2.13.10's main, it takes 9 seconds!
Horrible.

I guess I could use Catch2 v3 but that means adding a `-l` to my compilation flags everytime I want to use it which is a slight annoyance.

**SysAdmin Note:** For future reference, I just stuck the `catch.hpp` header into `/usr/local/include` and GCC was automatically able to pick it up without extra environment variable mucking.

# 20230213
Another reading-only day.
Pretty scattered reading going through Lospinoso's C++ Book and some Hacker News.
Nothing too deep or memorable.

I think I will probably just stick to the hard deadlines for Advent of Code of doing one per day.
Missed days are missed days.
The dragging on for this has been demoralizing.

Either we solve them or we move on the other problem sets.

# 20230212
Only read today, got a hang of the Jupyter architecture by reading a pretty nifty architecture doc.

Tried to make a client for Jupyter last year but got stuck in the weeds with all the moving parts.

Main takeaway is that there are three main components:
- The client
- The server
- The kernel

The server and kernel communicate with 0MQ while the client and server communicate with HTTP/WebSockets.
Seems like I didn't know what I was doing last time since I thought I would need 0MQ for the client.
I guess if I self-hosted the kernel, then I might have needed it?

Not sure, am still tentatively interested in writing a client but unclear how it fits into my priorities and overall goals.

# 20230211
Was out most of the day.
Managed to get to the next image in Ray Tracing in One Weekend but didn't read further into Advent of Code.

# 20230210
## Advent of Code 2022 - Voxel Surface Area
Did part 1 super quick.
Then got stumped by part 2.
Brainstormed some ideas, none of which I think were great
- Flood fill the air pockets and count as normal
  - Pro: Simple
  - Con: No way to know if we're flood filling the exterior or an air pocket (at least not off the top of my head)
  - Con: Makes our problem real dense and makes us count a lot of voxels.
- Do a connected components and find the surface areas of each by tracing a ray across every pixel on every face of the bounding cube. (I swear this makes more sense in my head than it does written)
  - Pro: I think it should work and be pretty quick?
  - Con: Coding the intersection checking seems hard.

Fatigue was setting so didn't end up going through with this one.

Might be easier to prototype this through the 2D case first? I think the 1D case is too trivial (and possibly poorly defined since the concept of interior and exterior in 1D are ambiguous).

Put some of my progress onto the [d18p2](https://github.com/jamcag/aoc22/tree/d18p2) branch.

## Ray Tracing in One Weekend
Decided to pick this up.
I've stumbled upon it before and have had false starts going through it.
This time, I'm just going through the code and skimming the prose.
At the end, they give some challenge extensions that I think will be super educational to do.
There should be a couple that have solutions to them too that I can compare against.

Made it to Listing 50 in coding and pushed my copy to [jamcag/rtweekend](https://github.com/jamcag/rtweekend).

# 20230209
## Advent of Code 2022 - Tetris
Did part 1 of day 17.
Was stuck for a while until I wrote a simple visualizer.
I relented and just did the visualization in Python ([pixel_preview](https://github.com/jamcag/pixel_preview/blob/main/Pixel%20Preview.ipynb)).
I tried making a PPM version but the image generated was too small, I didn't have a PPM viewer installed and the online one I tried had bad zooming capabilities.

Have some ugly copypasta in my solution.
Could easily refactor that into a function.

Will need to be smarter for part 2 since it has a massive number of shapes.
Store the border of my shape I guess?
I can't just store the heights since a piece could move horizontally into a gap in the middle.

## [`pixel_preview`](https://github.com/jamcag/pixel_preview/blob/main/Pixel%20Preview.ipynb) - Pixel Visualizer
Wrote the visualizer real quick using [JupyterLite Retro](https://jupyterlite.readthedocs.io/en/latest/_static/retro/tree/).
It's pretty nice to just whip a quick visualization up especially when logging Python-parsable output from C++.

# 20230208
## Advent of Code - Multiagent Valve Opening Pause
Officially giving up on the multi-agent valve opening problem.
I know a path forward but it involves a lot of copy and paste.
Would be interesting to return to this one and see if I can think of a better way to write it.

In essence it's a graph search problem which shouldn't be that hard.
Writing out the states to handle two agents is pretty tedious though to handle all of the edge cases.

My furthest attempt was in [this Jupyter notebook](https://github.com/jamcag/aoc22/blob/c3a9321a6ae93912c45665afe4235e5c3f5c7406/16_py/Multi-Agent.ipynb) where I started to work through the cases.

## Fastbook Chapter 1
Read through Chapter 1 of Fastbook.
The material was pretty much the same as lecture 1 so didn't learn anything new.

# Deep Learning for Coders Homework 1
I trained an orange tabby vs. ragdoll cat breed classifier.

Gross.

The original bird/not-bird classifier got close to 100% accuracy after three epochs but I was seeing 64% accuracy.
Another two epochs brought this down to 90% where it stayed that way.

# 20230207
Trying to modify my single-agent valve opening code to handle multi-agent became a bit hairy so I decided to re-write in Python.
The Python re-write isn't finished though.

# 20230206
Only did the case analysis for multiagent valve opening today.

# 20230205
## `mk`
Wrote a small C++ program to compile programs with `-std=c++20` by default.

My workflow is usually doing `make <target>` but this by default uses the default C++ standard of the installed GCC.

Fixes a very minor annoyance with about five minutes of coding.
Would have been 5 seconds in Python but C++ everything.

## Advent of Code - Multiagent Valve Opening
Came up with a rough sketch of how to solve AoC '22 Day 16 Part 2.
Started pretty late and feeling like I'll write a lot of bugs if I try to implement it now.
Cop out?
Probably.

## CPP Syntax 2
Watched Herb Sutter's talk on Syntax 2.
Pretty thought-provoking and would be nice if the language evolved that way but I'm not going to go out and learn an experimental language.

# 20230204
**Note** This is a laterblog.

## Advent of Code - Finished Valve Opening
Finally finished this one.
Main problem was that I was using the wrong data structure for BFS so my shortest paths were wrong.
Noticed this when I generated Graphviz visualizations of my graphs and saw non-symmetric edge weights.

### BFS Data Structure
BFS uses queues.
I used to have an Anki card for this but I stopped using spaced repetition.
Intuitively, with BFS we want to expand earlier expanded nodes before later expanded nodes so a queue makes sense.

Mnemonically, **B** comes before **D** and **Q** comes before **S** so **B**FS:**Q**ueue :: **D**FS:**S**tack.

### Future Improvements
Overall unhappy with the code and my approach.

There's a weird mix of trying to cache results for path finding and using those results in a depth-first search.
I don't think the outer depth-first search loop even works.
The main point of doing DFS in the outer loop was to limit memory use though since I was worried about running out back before I tried compressing the search down to just positive-rate valves.

The graph size ultimately didn't matter so doing the outer loop with BFS worked just fine.

Problem-solving wise, I approached this problem horrendously.
I first implemented a greedy-algorithm I was almost certain wouldn't work.
Then I built up dynamic programming in my head as the solution with no good justification then went down a rabbit hole of pre-reading a bunch of resources on dynamic programming.
Then I came back to the problem, and decided I could reduce the search size but didn't get to coding it for a while.

I spent a day implementing (broken) shortest paths.
Then not getting the right answer, I procrastinated the next day on debugging.

Being more selective in what I was printing out was a good reset when reading massive logs felt hopeless.
Then, writing the Graphviz export was really helpful with unblocking and finding the root cause.

**Actions**
- Practice identifying when dynamic programming is good for a problem.
- Think about ways to reduce search graphs early on 
  - This was also pretty important for finding the needle in the haystack back in the L1-norm sensors and beacons problem
- Delete log statements that are no longer needed
  - This would be easier to do if I followed through with writing a debug utility function
  - Main concern with deleting existing logging code is that it's tedious to re-add it again.
  - This could probably be solved with version control but in my experience doing very small checkpoints like this is still pretty high effort to restore past checkpoints compared to just re-typing the logging
- Visualize when appropriate
  - Example: A log for a single graph node expansion is starting to span at least 20 lines, or the overall log for a problem exceeds 10k lines.
- Think of ways to actively unblock myself faster


### Valve Opening Part 2
Part 2 of this problem involves adding another agent which might be able to do things.
I decided to not finish it today since doing the first part was exhausting.

# 20230203
## Advent of Code - Pathfinding for Valve Opening
Wrote the pathfinder to calculate costs to open closed valves for a given state.
Just need to tie the main loop together from here.

## Handling Third Party C++ Dependencies
Because of how slow `FetchContent` is and how submodules take a while to initialize, I'm going to be checking in my dependencies for C++ projects.
To keep the GitHub language stats accurate, I'll put the dependencies into a `lib/` directory and in `.gitattributes` I'll put
```
lib/ linguist-vendored
```

## Idea: Utility Header
I find myself writing a lot of `std::cout << "var=" << var;` statements and it would be nice to put this into a function I could just call.


# 20230202
## Advent of Code - Opening Small Valves
I think I came up with the solution method, to keep the search graph smaller we compress the graph and replace runs of zero-rate valves with weighted edges.
This way, we only make a decision (and add a new node to our search tree) at the beginning node "AA" and at nodes that have non-zero rate.
The tree is also bounded by length.

Started implementing this but there's a lot of pieces so I didn't manage to finish.

## `mkcmk` - CMakeLists.txt Generator
I often find myself wanting to open m solutions in CLion which wants a CMake project so I made a simple generator.
C++ is definitely not the *nicest* language for this, but given that buildozer and a bunch of other string processing tools are written in Go, I think it will be instructive to try and do it in C++.

# 20230201
## Advent of Code - Opening Valves Small Example
Didn't make much progress on this but did come up with a minimal example with 4 nodes over 7 time steps that my greedy algorithm fails on.
Still have a feeling it's some form of dynamic programming to solve which I don't understand.

Searching through the ~2^30 states might be fast enough?
Maybe I should just try it.

## MIT Algorithms 2020 - Lecture 15: Dynamic Programming
Watched Erik Demaine's [lecture](https://www.youtube.com/watch?v=r4-cftqTcdI) ([notes](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/resources/mit6_006s20_lec15/)) on Dynamic Programming.
It's probably the best introduction I've seen since they make it very systematic with their SRTBOT mnemonic.
They apply it to Fibonacci, DAG single-source shortest paths, and a toy bowling problem.

I think the main gem was for sequence inputs the main subproblems to try to decompose to are suffixes, prefixes, and substrings.
They have another three lectures just on dynamic programming alone which might be worth going through.

# 20230131
## Advent of Code - Opening Valves
Pretty sweaty with the day 16 problem.
It's looking like dynamic programming so the panic set in pretty quick.
Tried thinking of it like a decision process to solve with reward maximization but I'm not clear on how to solve that either.

Wrote an ugly input reader as per usual with these.

Then tried to see how a greedy approach would fare.
I go to the best reward-over-time valve and open it at each time step.
This way of modelling it does not account for how going to the *best* reward at the time affects how much further it puts me from further away rewards.

Exhaustive search will probably not do very well since with a time horizon of 30 steps, I'll have a large number of states to search through.
## USB Transfer Speeds
Collecting some stats on transfer speeds I'm seeing on my USB drive.
Noticing about 130 MiB/s when it's connected by USB 3.1 Gen 1 port on my laptop, and about 15 MiB/s to the USB 2.0 port on my desktop.
The drive is rated for 150 MiB/s so there's a gap there.

## Algorithm Design Manual - Dynamic Programming Fibonacci
I read some pages of Skiena's Algorithm Design Manual Chapter 8 which covers dynamic programming.
Their description of dynamic programming isn't super intuitive but they do say that once you understand it, that it becomes one of the easiest to implement.
I read the chapter up to "8.1.3 Fibonacci Numbers by Dynamic Programming".

# 20230130
Even running part 2 overnight didn't work.
Busting out the profiler.
Had to set Linux up to allow running perf without `sudo`. 
```
sudo sh -c 'echo 1 > /proc/sys/kernel/perf_event_paranoid'
sudo sh -c 'echo 0 > /proc/sys/kernel/kptr_restrict'
```

Also did some timing for the first time.
For future reference:
- `#include <chrono>`
- `std::chrono::high_resolution_clock::now()` to measure time points
- Cast the difference between time points to a double to get the time delta in seconds

**Update:** Finally got it.
Got the solution on a walk that we don't need to check all the possible squares, instead we only need to check grid cells just outside the circles.
This reduces our search space to about 0.1% of the cells.

# 20230129
## Advent of Code 2022 - Sensors and Beacons
Did day 15 part 1.

The parsing is really ugly.
I tried to do it with regex first which would have been better but I could not get capture groups to work right.

Turns out `std::find(container.begin(), container.end(), elem)` is not equivalent to `container.find(elem)`.
The former is always a linear search while the latter exploits the internal data structures.
This is a major surprise since I think a lot of my solutions do it the slow way.
My falling sand solution taking just under 5 minutes also uses the slow way, switching from `vector` to `set` to `unordered_set` made no difference since I was always using `std::find`.

Also, emplacing into a `vector` made my code a lot faster than when I was putting it into a `set`.
My solution was not finishing under 5 minutes when I was using a set but did just fine with a vector.

## Fast AI 2022 Lecture 1
Went through the whole lecture of Fast AI 2022, I think I watched parts of it before.
He opens up with an example of training a bird detector, goes into a segue into teaching philosophy, and then does it again in more detail.
After that, we looked at doing other tasks such as segmentation and tabular learning.
Homework is to read the first chapter of the book and think of a cool project.

The library itself seems pretty straightforward and powerful.
The key concept is a `DataBlock` where you specify the input and output types, any transforms, how to randomly split data, and give functions to load data and labels.
The DataBlock then gives you PyTorch `DataLoader`s that load data fast in parallel to feed the GPU so that it's always processing.

From there, the library has different "learners" for different tasks.
For example, there's a vision learner for image classification, a "UNet"-learner for segmentation, and a tabular learner for tabular learning.

A learner combines the data you specify and a model from the library (i.e. ResNet for vision).
Most of the time the model has pre-trained weights.
Tabular learning models don't usually come pre-trained though since transfer learning is apparently not as useful yet for this domain.

He also showed a cool example of multiple dispatch where no matter what was inside a `DataBlock` the method to show samples of the data would always return the right thing (i.e. image previews vs. rows of a table vs. segmentation masks).
I'm still not clear on what multiple dispatch is, but if I ever dive deeper into it I should come back to these examples.

# 20230128

## Advent of Code 2022 Day 13 - Falling Sand ~~Game~~
Did part 1 after much handwringing.
My part 2 solution is very slow.
The approach is likely very different.
To make search faster, I tried moving from a `vector` $(O(n))$ to a `set` $(O(\log n))$ to an `unordered_set` $(O(1))$ but it still is taking longer than five minutes to complete.

For the final settled shape of part 2, we'll likely end up with a triangle but will need to subtract off any unreachable cells.

**Update:** Re-running it with `-O3` optimization and it caught up to whatever optimization level CLion was running at in about 3 minutes.
The CLion one was 30 minutes into running.
It's still really slow but it would be interesting to understand what optimization did to make it so much faster (10x!).

**Update 2:** It finished under 5 minutes (4m37s to be exact) so I'll take it and go.

**Update 3:** I watched someone else's solution and they adapted their part 1 solution pretty directly so I think it should still be the same approach.
Their code was pretty hard to grok though so I didn't get into the details of how they solved it.

## Qt and gRPC Setup
I think I want to do `tviz` inside of Qt instead and add networking so that the text source doesn't have to run in the same process.
I initially set up my CMake project to download gRPC using CMake but it was taking about 10 minutes to run `cmake ..` mostly on just cloning the `grpc` repo and its submodules so I instead just built it before hand and installed gRPC and Protobuf into `opt`.
Doing this with CMake-built projects is pretty easy as you just define the `CMAKE_INSTALL_PREFIX` flag when configuring the build environment.

Adding `/opt/protobuf` and `/opt/grpc` to `CMAKE_PREFIX_PATH` then lets my project succeed with the `find_package` calls.

I still don't have it set up to build and link `.proto` files automatically but I can probably avoid it and pre-build it and check the outputs in for now as the data and RPC calls likely won't change too much given the simplicity of the application.

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


## LeetCode - Number of Steps
(Laterblog 20230127)

This one seemed like a variation of rope cutting that's used to introduce dynamic programming.
First did a naive solution recursion-only (no DP) solution which I thought would work.
It worked on the simple test cases of $n=2$ and $n=3$ but failed for $n=4$ which should have five ways.
Then relented and did a DP solution.

To refresh, I coded up a quick Fibonacci calculator which was pretty straightforward.
Then just tried out `return num_steps(n - 2) + num_steps(n - 1)` in my solution and it somehow worked.
I was expecting some constant (either 1 or 2) to be added too but just doing the recursive calls seemed to be enough to pass the test cases.

Performance was pretty bad with 30% in runtime and memory usage too but haven't given it more thought.

## LeetCode - Longest Palindrome
(Laterblog 20230127)

Was initially stumped on this one trying to think of how to calculate palindromes but I think the easy peasy method should be good enough.
Basically, for every substring $s_i:s_n$ of the string $s$ you check if $s_i:s_j$ is a palindrome and find the largest $j$ which is.
This gives you all the possible palindromes and then it's just a simple max.

Ran into some errors coding it up but haven't went back to it since.
Not sure what the optimal solution is yet either.

We'll pick it back up.
I think once we're done Advent of Code 2022 we can start looking at LeetCode problems more systematically.

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
