## Bazel Sample Project (Java)

### Approach
Generate Java Source Files from XSD schema within the Bazel Build Tool
and use these generated Sources to build a Message Library, which can be
used again as Dependency in other Libraries or Binaries.

### Problem
The Library can't be build with the first run, because there are no Java Source
Files yet under the 'src/' Directory and bazel doesn't like that.

### Output
#### 1st call
```
$ bazel build //...
WARNING: ignoring http_proxy in environment.
ERROR: (11-29 15:14:39.519) ~/bazel-xjc/BUILD.bazel:27:12: in deps attribute of java_library rule //:sample-lib: deps not allowed without srcs; move to runtime_deps?
WARNING: (11-29 15:14:39.539) errors encountered while analyzing target '//:sample-lib': it will not be built.
INFO: (11-29 15:14:39.539) Analysis succeeded for only 1 of 2 top-level targets
INFO: (11-29 15:14:39.541) Found 1 target...
WARNING: (11-29 15:14:40.509) ~/bazel-xjc/BUILD.bazel:1:1: input 'src' to //:sample-messages is a directory; dependency checking of directories is unsound.
INFO: (11-29 15:14:40.925) From Executing genrule //:sample-messages:
INFO: Generating to ./src
Target //:sample-messages up-to-date:
  bazel-genfiles/out_sample-messages
ERROR: (11-29 15:14:40.958) command succeeded, but not all targets were analyzed.
INFO: (11-29 15:14:40.959) Elapsed time: 2.394s, Critical Path: 0.42s

```
#### 1st call results
```
$ ls -la src/org/acme/sample/
.
..
ObjectFactory.java
Sample.java
```
```
$ ls -la bazel-bin/
.
..
```
```
$ cat bazel-genfiles/out_sample-messages 
parsing a schema...
compiling a schema...
[INFO] generating code
unknown location

org/acme/sample/ObjectFactory.java
org/acme/sample/Sample.java
```
#### 2nd call
```
$ bazel build //...
WARNING: ignoring http_proxy in environment.
INFO: (11-29 15:20:02.133) Found 2 targets...
WARNING: (11-29 15:20:03.215) ~/bazel-xjc/BUILD.bazel:1:1: input 'src' to //:sample-messages is a directory; dependency checking of directories is unsound.
INFO: (11-29 15:20:03.670) From Executing genrule //:sample-messages:
INFO: Generating to ./src
INFO: (11-29 15:20:03.994) Elapsed time: 2.912s, Critical Path: 0.75s
```
#### 2nd call results
```
$ ls -la bazel-bin/
.
..
_javac
libsample-lib.jar
libsample-lib.jar-2.params
libsample-lib.jar_manifest_proto
libsample-lib.jdeps
```
```
$ jar -tf bazel-bin/libsample-lib.jar 
META-INF/
META-INF/MANIFEST.MF
org/
org/acme/
org/acme/sample/
org/acme/sample/ObjectFactory.class
org/acme/sample/Sample$Content.class
org/acme/sample/Sample.class
```
