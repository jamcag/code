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
