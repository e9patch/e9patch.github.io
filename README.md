# E9Patch: Binary Rewriting without Control Flow Recovery (Tool demo)

-------

This document contains instructions for obtaining and executing a demo version of E9patch. Please
note the following:

  1. This is an early and incomplete version of E9Patch that is intended to demonstrate the overall design
of the framework.
  2. If the paper is accepted, a full version with the documentation and an experiments reproduction package
will be definitely released for the use of communities.
  3. The demo is distributed via an anonymous Docker image (the size is around 1GB).
  4. We do not include the source code of E9Patch since it may be identity-revealing.

## Run
In order to run E9Patch, we recommend to create a temporary docker container using the following command
(the container is automatically removed on exit):

```
docker run --rm -ti e9patch/e9patchdemo /bin/bash
```

Once the shell is launched, execution the following commands to run e9patch.demo

```
cd e9patch.demo
set +H
./e9patch.demo "(?!nop|lea).*[^\x3A]\((?!%rip|%rsp).*\).*" `which xterm` xterm_new
```

In the above command, e9patch instruments all the memory access ((?!nop|lea).\*[^\x3A]\((?!%rip|%rsp).\*\).\*) instructions 
for xterm program, and generates an instrumented program *xterm_new*.

## Output

E9patch will output the statistic information of the instrumentation. Just as follows:

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

TODO: explain the results
