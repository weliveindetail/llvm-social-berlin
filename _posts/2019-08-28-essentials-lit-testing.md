# LLVM Social #18

Meetup Event: https://www.meetup.com/de-DE/LLVM-Social-Berlin/events/263394131/

## From the weeklies

* Gábor Horváth gave an update on adding lifetime analysis to Clang: <br>
  http://lists.llvm.org/pipermail/cfe-dev/2019-August/063164.html <br>
  https://github.com/isocpp/CppCoreGuidelines/blob/master/docs/Lifetime.pdf

## Essentials: LIT testing

See [slides as PDF here](../_material/2019-08-28/essentials-lit-testing.pdf) and notes on [Tools &amp; Sources below](#tools-and-sources).

### Build LLVM 9

```
$ cmake -G Ninja -B llvm-build -S llvm-project/llvm -DCMAKE_BUILD_TYPE=Debug -DLLVM_ENABLE_PROJECTS="clang;libcxx;lldb" -DLLVM_TARGETS_TO_BUILD=host
$ ninja -C llvm-build check-llvm
[2134/2135] Running the LLVM regression tests
Testing Time: 324.21s
  Expected Passes    : 19116
  Expected Failures  : 57
  Unsupported Tests  : 14020
```

### Build JitFromScratch against LLVM 9

```
$ cmake -G Ninja -B jitfromscratch-build -S jitfromscratch -DLLVM_DIR=/path/to/llvm-build/lib/cmake/llvm
$ ninja -C jitfromscratch-build check
[5/6] Running tests
-- Testing: 3 tests, 3 threads --
PASS: JitFromScratch :: stderr.test-debug (1 of 3)
PASS: JitFromScratch :: stdout.test (2 of 3)
PASS: JitFromScratch :: objcache.test-debug (3 of 3)
Testing Time: 0.45s
  Expected Passes    : 3
```

### Implementation From Scratch

* The `check` target: https://github.com/weliveindetail/JitFromScratch/commit/d84cc78#diff-15547c54d3d4898a882b4ab7b3cee381R48
* Main config `lit.cfg.py`: https://github.com/weliveindetail/JitFromScratch/blob/llvm09/master/test/lit.cfg.py
* Site config `lit.site.cfg.py.in`: https://github.com/weliveindetail/JitFromScratch/blob/llvm09/master/test/lit.site.cfg.py.in
* Simple test `stdout.test`: https://github.com/weliveindetail/JitFromScratch/blob/llvm09/master/test/stdout.test
* Multi-run test `objcache.test-debug`: https://github.com/weliveindetail/JitFromScratch/blob/llvm09/master/test/objcache.test-debug

### Real-world examples

* JITLoaderGDB test: https://reviews.llvm.org/D61611#change-hXAU7ibOzJzb
* Clang tests: https://github.com/llvm/llvm-project/blob/master/clang-tools-extra/test/clang-tidy/readability-const-return-type.cpp and https://github.com/llvm/llvm-project/blob/master/clang/test/Parser/brackets.cpp
* clang-tidy test generator: https://github.com/llvm/llvm-project/blob/master/clang-tools-extra/clang-tidy/add_new_check.py

## git-baobab: LLVM Release changes
```
$ git clone https://github.com/weliveindetail/git-baobab.git $ROOT/git-baobab
$ cd $ROOT/git-baobab
$ git rev-parse HEAD
e6f387cc316f87d17a62012af7df7bd1bebe5248

$ ln -s $ROOT/git-baobab/git-baobab /usr/local/bin/git-baobab

$ cd $ROOT/llvm-project
$ git checkout release/9.x
$ git merge-base HEAD origin/release/8.x
7b5565418f4d6e113ba805dad40d471d23bca6f6
$ git baobab 7b5565418f4d --cpp -exclude "/(test|unittest|unittests)/"
...
```

[LLVM 8](../_material/2019-08-28/llvm-8.html) -
[LLVM 9](../_material/2019-08-28/llvm-9.html) -
[LLVM 9 since branch](../_material/2019-08-28/llvm-9-since-branch.html)

<a name="tools-and-sources"></a>

## Tools &amp; Sources

```
$ ROOT=$(pwd)

$ git clone https://github.com/weliveindetail/JitFromScratch $ROOT/jitfromscratch
$ cd $ROOT/jitfromscratch
$ git checkout llvm09/master
$ git rev-parse HEAD
075ea433a0c6acb4a781c1d73ac0636547d7f0e0

$ git clone https://github.com/llvm/llvm-project $ROOT/llvm-project
$ cd $ROOT/llvm-project
$ git checkout release/9.x
$ git rev-parse HEAD
e82a53603ae3fed2215a44b5ac603db00a780c02

$ git --version
git version 2.21.0
$ cmake --version
cmake version 3.13.4
$ ninja --version
1.9.0
$ clang --version
Apple LLVM version 10.0.0 (clang-1000.11.45.5)
```
