# FoRTE-Research's AFL for Fixed Input Datasets

This repository contains several modified versions of AFL components which we utilize in our experiments on fixed (pre-generated) fuzzing input datasets: 
* **afl-fuzz-saveinputs** - dumps AFL-generated inputs and their sizes to file (for fixed input dataset experiments).
* **testtrace** - evaluates any tracer's (e.g., QEMU, Dyninst) execution time on a fixed input dataset.
* **afl-cc forkserver-only extension** - inserts *only* a forkserver during AFL's assembly-time instrumentation (useful for benchmarking).

<table>
  <tr>
    <td align=center colspan="2"><div><b>Presented in our paper</b> <a href="https://arxiv.org/abs/1812.11875"><i>Full-speed Fuzzing: Reducing Fuzzing Overhead through Coverage-guided Tracing</i></a><br>(to appear in the 2019 IEEE Symposium on Security and Privacy).</td>
  </tr>
  <tr>
    <td><b>Citing this repository:</b></td>
    <td>
      <code class="rich-diff-level-one">@inproceedings{nagy:fullspeedfuzzing,</code><br>
      <code class="rich-diff-level-one">title = {Full-speed Fuzzing: Reducing Fuzzing Overhead through Coverage-guided Tracing},</code><br>
      <code class="rich-diff-level-one">author = {Stefan Nagy and Matthew Hicks},</code><br>
      <code class="rich-diff-level-one">booktitle = {{IEEE} Symposium on Security and Privacy (Oakland)},</code><br>
      <code class="rich-diff-level-one">year = {2019},}</code>
    </td>
  </tr>
  <tr>
    <td><b>Developer:</b></td>
    <td>Stefan Nagy (<a href="mailto:snagy2@vt.edu">snagy2@vt.edu</a>) and Matthew Hicks (<a href="mailto:mdhicks2@vt.edu">mdhicks2@vt.edu</a>) </td>
  </tr>
  <tr>
    <td><b>License:</b></td>
    <td><a href="/FoRTE-Research/UnTracer-AFL/blob/master/LICENSE">MIT License</a></td>
  </tr>
  <tr>
    <td><b>Disclaimer:</b></td>
    <td><i>This software is strictly a research prototype.</i></td>
  </tr>
</table>

## INSTALLATION
#### 1. Download and build AFL:
```
git clone https://github.com/FoRTE-Research/afl-fid
cd afl
make all
```

#### 2. (optional) Build AFL's QEMU-mode tracing:
```
sudo apt-get install libtool-bin libglib2.0-dev automake flex bison
cd afl-fid/qemu_mode
sudo ./build_qemu_support.sh
chmod +x ../afl-qemu-trace
```
Note that the build may finish with an error even though `afl-qemu-trace` was built correctly. We recommend checking a few build status messages in the log to determine if the build was successful.

## afl-fuzz-saveinputs
`afl-fuzz-saveinputs` is a modified version of `afl-fuzz` for dumping generated inputs to file. Usage is as follows:
```
afl-fuzz-saveinputs -i [/path/to/seed_dir] -o [/path/to/out_dir] -e [time budget (# minutes)] [optional_args] -Q -- [/path/to/target] [target_args]
```
Input dump and sizes will be stored in `out_dir/_INPUT_DUMP` and `out_dir/_INPUT_SIZES`, respectively.  
 * **Note:** QEMU mode is recommended, otherwise dumps may be explosively large in size (depending on fuzzing speed).

## testtrace
`testtrace` is an `afl-fuzz` modification for logging each fuzzed input's tracing time. Given an input dump and corresponding sizes file (produced by `afl-fuzz-saveinputs`), It recreates each input and logs its execution (function `run_target()` in `afl-fuzz`) time. Usage is as follows:
```
testrace -i [/path/to/input/data/dump] -s [/path/to/input/sizes/dump] -o [/path/to/outdir] -f [/path/to/outfile] -c [max execs | skip for full dump] -t [exec timeout | skip for default (100ms)] -- [/path/to/target] [target_args]
```
 * **Note:** only non-position-independent target binaries are supported. Be sure to compile all target binaries with the `-no-pie` compiler flag (unnecessary for Clang).
 
## afl-cc forkserver-only extension
We extend the `afl-cc` assembly-time instrumentation with a forkserver-only instrumentation mode for use in benchmarking experiments. To invoke this mode, simply append `-Wa,-F` to your `afl-cc` (e.g., `afl-gcc`, `afl-clang`) compiler flags and compile as usual. Example:
```
./configure --disable-shared CC=afl-clang CXX=afl-clang++ CFLAGS="-g -O2 -no-pie -Wa,F" CXXFLAGS="-g -O2 -no-pie -Wa,-F"
make all
```

#
<p align=center> <a href="https://www.cs.vt.edu"><img border="0" src="http://people.cs.vt.edu/snagy2/img/vt_inline_computer_science.png" width="60%" height="60%">
</a> </p>
