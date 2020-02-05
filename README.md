# E9Patch Tool Demo
## Binary Rewriting without Control Flow Recovery

-------

This document contains instructions for obtaining and executing a demo
version of E9patch.
The demo implements a fixed application in the form of
*instruction-printing instrumentation*.

Please note the following:

  1. This is an incomplete demo version of E9Patch that implements
  a single fixed application.
  The demo is primarily designed for testing purposes, and
  does not reflect the final design of E9patch nor the
  final tool API.
  2. The demo should be considered alpha-quality software.
  3. The full version of E9patch will be released (open sourced) before the
  conference in June 2020.
  4. The demo is distributed via an anonymous Docker image (the size is around 700MB).
  5. We do not include the source code of E9Patch in this distribution since it may be identity-revealing.

## Description

The demo uses the E9patch binary rewriting engine to instrument
selected instructions with code that prints a string representation of
the instruction as it is executed.
The basic usage of the demo tool is as follows:

        ./e9patch.demo reg-exp input-binary output-binary

where:

* `reg-exp` is a regular expression used to specify which instructions
   should be instrumented.
* `input-binary` is the path to the input binary
* `output-binary` is the path to the output (instrumented) binary.

For example, the command:

        ./e9patch.demo 'j.*' /bin/bash a.out

will instrument all instructions matching `j.*` in the `/bin/bash` binary,
and write the modified binary out to `a.out`.

Running `a.out` will execute the instrumented version of bash that prints
all instructions matching `j.*` (primarily jump instructions).

        ...
        jle 454
        jle -331
        jbe 12
        je -1735
        user@host:~$ je 125

## Running the Demo

In order to run the E9patch demo, please create a temporary docker container using the following command:

        docker run --rm -ti e9patch/e9patchdemo /bin/bash

It will automatically download the docker image from docker hub and create a docker container by executing a bash shell.
Once the shell is launched, execution the following commands to run e9patch.demo

        cd e9patch.demo
         set +H
        ./e9patch.demo 'j.*' `which bash` a.out

To run the instrumented binary, simply run:

        ./a.out

The instrumented version should behave the same as normal `bash`, but print a
lot of additional output to `stderr` (as shown above).

Note that it is possible to try other regular expressions and input binaries.

### Output

E9patch will output the statistical information about the instrumentation.
For the example above, the following output should be generated:

        ...
        input           = /bin/bash
        num_mappings    = 9937
        num_T0          = 21199 / 26264 = 80.72%
        num_T1          = 2879 / 26264 = 10.96%
        num_T2          = 589 / 26264 = 2.24%
        num_T3          = 1597 / 26264 = 6.08%
        old_size        = 1037528bytes
        new_size        = 3203261bytes (308.74%)

Here:

* `input` is the input binary
* `num_mappings` is the number of mappings used
  (after *physical page grouping* is applied)
* `num_T0` is the number of instrumentation using the baseline
   tactics (jumps `B1` and punned jumps `B2` from the submission)
* `num_T1`/`num_T2`/`num_T3` is the number of instrumentation using
   tactics `T1`/`T2`/`T3` respectively.
* `old_size` is the input binary size.
* `new_size` is the output binary size.

Note that the output binaries are generally larger than those reported
in the submission (Table 1).
This is simply because the instruction printing instrumentation itself is
larger.

In this example, E9patch can instrument 100% of all instructions.
Note that the baseline instruction punning (`T0`) can only achieve
80.72% coverage, meaning that 5065/26264=19.28% of all instructions
would go uninstrumented.
This highlights the importance of the `T1`/`T2`/`T3` tactics.
