# E9Patch: Binary Rewriting without Control Flow Recovery (Tool demo)

-------

This document contains instructions for obtaining and executing a demo version of E9patch. Please
note the following:

  1. This is an early and incomplete version of E9Patch that is intended to demonstrate the overall design
of the framework.
  2. If the paper is accepted, a full version with the documentation and an experiments reproduction package
will be definitely released for the use of communities.
  3. The demo is distributed via an anonymous Docker image (the size is around 700MB).
  4. We do not include the source code of E9Patch since it may be identity-revealing.

## Run
In order to run E9Patch, please create a temporary docker container using the following command:

```
docker run --rm -ti e9patch/e9patchdemo /bin/bash
```

It will automatically download the docker image from docker hub and create a docker container by executing a bash shell.
Once the shell is launched, execution the following commands to run e9patch.demo

```
cd e9patch.demo
set +H
./e9patch.demo "(?!nop|lea).*[^\x3A]\((?!%rip|%rsp).*\).*" /usr/bin/xterm xterm_new
```

In the above command, e9patch instruments all the memory access ((?!nop|lea).\*[^\x3A]\((?!%rip|%rsp).\*\).\*) instructions 
of xterm program, and generates an instrumented program *xterm_new*. The demoed instrumentation will print the instrumented instrutions.

## Output

E9patch will output the statistical information of the instrumentation. Just as follows:

```
Building physical page layout...done.
Physical page grouping...done.
Adding patch pages...done.
input           = /usr/bin/xterm
num_mappings    = 1918
num_T0          = 9267 / 10864 = 85.30%
num_T1          = 1278 / 10864 = 11.76%
num_T2          = 62 / 10864 = 0.57%
num_T3          = 257 / 10864 = 2.37%
old_size        = 543232bytes
new_size        = 1480804bytes (272.59%)
```

As shown in the results, e9patch achieves 100% coverage (T0+T1+T2+T3), i.e. it can instrument all the memory access instructions. In contract, the original *instruction puning* (T0) only achieves 85.3% coverage, meaning that it fails to instrument 1597 (10864-9267) instructions.


TODO: physical page management
