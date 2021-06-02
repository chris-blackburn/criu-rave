# RAVE: 

## Build RAVE

```
❯ git clone -b rave https://github.com/chris-blackburn/criu-rave.git
❯ cd criu-rave; git submodule update --init --progress
❯ cd rave
❯ cmake -B build
❯ cd build
❯ make
```
Next, build CRIU:
```
❯ cd ../..     # cd to the CRIU root dir
❯ make
```

## Running applications using RAVE
Start the test application (in `test/rave/`):
```
❯ ./test/rave/loop -n 10
pid: 31389, cnt: 10 
In function func_path_b.
In function func_path_a.
1 
2 
3 
```
In another terminal, checkpoint the `loop`:
```
❯ ./dump.sh loop
Warn  (compel/arch/x86/src/lib/infect.c:281): Will restore 31389 with interrupted system call
```
By default, `./dump.sh` will checkpoint the process into the `vanilla-dump` directory. Now, start the `criu lazy-pages` daemon:
```
❯ sudo ./criu/criu lazy-pages -D vanilla-dump
```
In another terminal, restore the `loop` process:
```
❯ sudo ./criu/criu restore -j -D vanilla-dump --lazy-pages
4 
5 
...
```

Some logs printed on the lazy-pages daemon:
```
❯ sudo ./criu/criu lazy-pages -D vanilla-dump
[rave] DEBUG (rave_init:87) Intializing rave with binary: /home/xiaoguang/works/randomization/criu-rave/test/rave/loop
[rave] DEBUG (binary_init:69) Initializing binary from file: /home/xiaoguang/works/randomization/criu-rave/test/rave/loop
[rave] FATAL (binary_init:108) rave only supports executable elfs
[rave] DEBUG (rave_close:134) Closing rave handle...
[rave] ERROR (rave_close:138) Couldn't unmap file memory
[rave] DEBUG (binary_close:139) Binary unloaded
```

## RAVE internal
TODO: Add how rave parses function boundaries and serves the randomized prologues and epilogues.