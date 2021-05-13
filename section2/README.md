
# 2章
## EDK II でハローワールド

```sh
$ git clone https://github.com/uchan-nos/mikanos.git
$ cd mikanos
$ git checkout osbook_day02a

$ cd ~/edk2
$ ln -s ~/mikanos/MikanLoaderPkg ./

$ source edksetup.sh
```

Conf/target.txt ファイルを変更する(`TOOL_CHAIN_TAG`は本と異なる)
| 項目            | 値                                |
|:----------------|:----------------------------------|
| ACTIVE_PLATFORM | MikanLoaderPkg/MikanLoaderPkg.dsc |
| TARGET          | DEBUG                             |
| TARGET_ARCH     | X64                               |
| TOOL_CHAIN_TAG  | CLANGPDB                          |


ビルドして実行
```sh
$ cd ~/edk2
$ build
$ cd Build/MikanLoaderX64/DEBUG_CLANGPDB/X64
$ ~/osbook/devenv/run_qemu.sh Loader.efi
```


## メモリマップの取得
ビルド
```sh
$ cd ~/mikanos
$ git checkout osbook_day02b
$ cd ~/edk2
$ source edksetup.sh
$ build
```

QEMU起動
```sh
$ cd Build/MikanLoaderX64/DEBUG_CLANGPDB/X64
$ ~/osbook/devenv/run_qemu.sh Loader.efi
```

マウント
```sh
$ mkdir -p mnt
$ hdiutil attach -mountpoint mnt disk.img
```

中身の確認 & アンマウント
```sh
$ ls mnt
$ cat mnt/memmap
$ hdiutil detach mnt
```

memmapの中見はこんな感じ
```
Index, Type, Type(name), PhysicalStart, NumberOfPages, Attribute
0, 3, EfiBootServicesCode, 00000000, 1, F
1, 7, EfiConventionalMemory, 00001000, 9F, F
2, 7, EfiConventionalMemory, 00100000, 700, F
3, A, EfiACPIMemoryNVS, 00800000, 8, F
4, 7, EfiConventionalMemory, 00808000, 8, F
5, A, EfiACPIMemoryNVS, 00810000, F0, F
6, 4, EfiBootServicesData, 00900000, B00, F
7, 7, EfiConventionalMemory, 01400000, 3AB36, F
                      ...
                      ...
                      ...
```

