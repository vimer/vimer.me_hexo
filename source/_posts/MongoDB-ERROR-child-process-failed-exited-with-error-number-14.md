title: "MongoDB ERROR: child process failed, exited with error number 14"
date: 2015-01-08 00:21:02
tags: [MongoDB,异常]
---
```javascript
about to fork child process, waiting until server is ready for connections.                                                                                             [121/1846]
forked process: 21522
Thu Jan  8 00:01:31.032 terminate() called, printing stack (if implemented for platform):
0xdd9e31 0x6cfb8e 0x7fd261647846 0x7fd261647873 0x7fd26164796e 0x7fd2615f4a07 0x7fd261604284 0x7fd2615f8a74 0x7fd2615f9b6b 0xdf75af 0xdf7efb 0xdf4ba0 0x9ead7f 0x6dde60 0x6dfc09 0
x7fd260ce176d 0x6cf979
./mongod(_ZN5mongo15printStackTraceERSo+0x21) [0xdd9e31]
./mongod(_ZN5mongo11myterminateEv+0x3e) [0x6cfb8e]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xb5846) [0x7fd261647846]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xb5873) [0x7fd261647873]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xb596e) [0x7fd26164796e]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZSt21__throw_runtime_errorPKc+0x57) [0x7fd2615f4a07]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x72284) [0x7fd261604284]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZNSt6locale5_ImplC1EPKcm+0x44) [0x7fd2615f8a74]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZNSt6localeC1EPKc+0x73b) [0x7fd2615f9b6b]
./mongod(_ZN5boost11filesystem34path21wchar_t_codecvt_facetEv+0x4f) [0xdf75af]
./mongod(_ZNK5boost11filesystem34path14root_directoryEv+0xbb) [0xdf7efb]
./mongod(_ZN5boost11filesystem38absoluteERKNS0_4pathES3_+0x40) [0xdf4ba0]
./mongod(_ZN5mongo27initializeServerGlobalStateEb+0x15f) [0x9ead7f]
./mongod() [0x6dde60]
./mongod(main+0x9) [0x6dfc09]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xed) [0x7fd260ce176d]
./mongod(__gxx_personality_v0+0x499) [0x6cf979]
Thu Jan  8 00:01:31.036 Got signal: 6 (Aborted).

Thu Jan  8 00:01:31.040 Backtrace:
0xdd9e31 0x6d0d09 0x7fd260cf64a0 0x7fd260cf6425 0x7fd260cf9b8b 0x6cfb93 0x7fd261647846 0x7fd261647873 0x7fd26164796e 0x7fd2615f4a07 0x7fd261604284 0x7fd2615f8a74 0x7fd2615f9b6b 0
xdf75af 0xdf7efb 0xdf4ba0 0x9ead7f 0x6dde60 0x6dfc09 0x7fd260ce176d
./mongod(_ZN5mongo15printStackTraceERSo+0x21) [0xdd9e31]
./mongod(_ZN5mongo10abruptQuitEi+0x399) [0x6d0d09]
/lib/x86_64-linux-gnu/libc.so.6(+0x364a0) [0x7fd260cf64a0]
/lib/x86_64-linux-gnu/libc.so.6(gsignal+0x35) [0x7fd260cf6425]
/lib/x86_64-linux-gnu/libc.so.6(abort+0x17b) [0x7fd260cf9b8b]
./mongod(_ZN5mongo11myterminateEv+0x43) [0x6cfb93]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xb5846) [0x7fd261647846]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xb5873) [0x7fd261647873]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xb596e) [0x7fd26164796e]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZSt21__throw_runtime_errorPKc+0x57) [0x7fd2615f4a07]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x72284) [0x7fd261604284]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZNSt6locale5_ImplC1EPKcm+0x44) [0x7fd2615f8a74]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZNSt6localeC1EPKc+0x73b) [0x7fd2615f9b6b]
./mongod(_ZN5boost11filesystem34path21wchar_t_codecvt_facetEv+0x4f) [0xdf75af]
./mongod(_ZNK5boost11filesystem34path14root_directoryEv+0xbb) [0xdf7efb]
./mongod(_ZN5boost11filesystem38absoluteERKNS0_4pathES3_+0x40) [0xdf4ba0]
./mongod(_ZN5mongo27initializeServerGlobalStateEb+0x15f) [0x9ead7f]
./mongod() [0x6dde60]
./mongod(main+0x9) [0x6dfc09]
./mongod(main+0x9) [0x6dfc09]
 /lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xed) [0x7fd260ce176d]

 ERROR: child process failed, exited with error number 14
```
看堆栈不容易看出问题,直接通过Google检索 'ERROR: child process failed, exited with error number 14' 可以试试
```
./mongod --repair
```
来修复这个问题 不过执行后可能会出现这个异常

```javascript
Thu Jan  8 00:02:44.615 [initandlisten] MongoDB starting : pid=21615 port=27017 dbpath=/data/db/ 64-bit host=AY130723091526426e0dZ
Thu Jan  8 00:02:44.616 [initandlisten] db version v2.4.5
Thu Jan  8 00:02:44.616 [initandlisten] git version: a2ddc68ba7c9cee17bfe69ed840383ec3506602b
Thu Jan  8 00:02:44.616 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
Thu Jan  8 00:02:44.616 [initandlisten] allocator: tcmalloc
Thu Jan  8 00:02:44.616 [initandlisten] options: { repair: true }

Thu Jan  8 00:02:44.622 [initandlisten] exception in initAndListen std::exception: locale::facet::_S_create_c_locale name not valid, terminating

Thu Jan  8 00:02:44.623 dbexit:
Thu Jan  8 00:02:44.623 [initandlisten] shutdown: going to close listening sockets...
Thu Jan  8 00:02:44.623 [initandlisten] shutdown: going to flush diaglog...
Thu Jan  8 00:02:44.623 [initandlisten] shutdown: going to close sockets...
Thu Jan  8 00:02:44.623 [initandlisten] shutdown: waiting for fs preallocator...
Thu Jan  8 00:02:44.623 [initandlisten] shutdown: closing all files...
Thu Jan  8 00:02:44.623 [initandlisten] closeAllFiles() finished
Thu Jan  8 00:02:44.623 [initandlisten] shutdown: removing fs lock...
Thu Jan  8 00:02:44.623 dbexit: really exiting now
```

会发现'exception in initAndListen std::exception: locale::facet::_S_create_c_locale name not valid, terminating' 这个异常, 说明mongodb不能正常运行于current locale,把LC_ALL设置为C, 即
```
export LC_ALL=C
```
就可以解决这个问题,并可以正常启动MongoDB了 :)

