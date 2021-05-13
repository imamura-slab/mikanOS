# mikanOS on Mac
ゼロからのOS自作入門　[内田公太・著] をMacでやろう!


# 環境
- MacBook Air (2020 Intel CPU)
- macOS Big Sur 11.2.3


# 参考サイト (ほぼ参考サイト通りに)
- [Mac で始める「ゼロからのOS自作入門」](https://qiita.com/yamoridon/items/4905765cc6e4f320c9b5)
- [MacでゼロからのOS自作入門をゼロから勉強中 - Zenn](https://zenn.dev/karaage0703/scraps/b2705131673377)


# 環境構築
### EDK II
```sh
$ cd $HOME
$ git clone https://github.com/tianocore/edk2.git
$ cd edk2
$ git checkout 38c8be123aced4cc8ad5c7e0da9121a181b94251
$ git submodule init
$ git submodule update
$ cd BaseTools/Source/C
$ make
```

### mikanos-build リポジトリの準備
```sh
$ cd $HOME
$ git clone https://github.com/uchan-nos/mikanos-build.git osbook
$ cd osbook/devenv
$ curl -L https://github.com/uchan-nos/mikanos-build/releases/download/v2.0/x86_64-elf.tar.gz | tar xz
$ cd ..
```

次の内容の mac.patch というファイルを作る
```sh
--- a/devenv/make_image.sh
+++ b/devenv/make_image.sh
@@ -23,11 +23,24 @@ qemu-img create -f raw $DISK_IMG 200M
 mkfs.fat -n 'MIKAN OS' -s 2 -f 2 -R 32 -F 32 $DISK_IMG

 $DEVENV_DIR/mount_image.sh $DISK_IMG $MOUNT_POINT
-sudo mkdir -p $MOUNT_POINT/EFI/BOOT
-sudo cp $EFI_FILE $MOUNT_POINT/EFI/BOOT/BOOTX64.EFI
+if [ `uname` = 'Darwin' ]; then
+    mkdir -p $MOUNT_POINT/EFI/BOOT
+    cp $EFI_FILE $MOUNT_POINT/EFI/BOOT/BOOTX64.EFI
+else
+    sudo mkdir -p $MOUNT_POINT/EFI/BOOT
+    sudo cp $EFI_FILE $MOUNT_POINT/EFI/BOOT/BOOTX64.EFI
+fi
 if [ "$ANOTHER_FILE" != "" ]
 then
-    sudo cp $ANOTHER_FILE $MOUNT_POINT/
+    if [ `uname` = 'Darwin' ]; then
+        cp $ANOTHER_FILE $MOUNT_POINT/
+    else
+        sudo cp $ANOTHER_FILE $MOUNT_POINT/
+    fi
 fi
 sleep 0.5
-sudo umount $MOUNT_POINT
+if [ `uname` = 'Darwin' ]; then
+    hdiutil detach $MOUNT_POINT
+else
+    sudo umount $MOUNT_POINT
+fi
diff --git a/devenv/mount_image.sh b/devenv/mount_image.sh
index ba8233e..aea4d7d 100755
--- a/devenv/mount_image.sh
+++ b/devenv/mount_image.sh
@@ -16,5 +16,9 @@ then
     exit 1
 fi

-mkdir -p $MOUNT_POINT
-sudo mount -o loop $DISK_IMG $MOUNT_POINT
+if [ `uname` = 'Darwin' ]; then
+    hdiutil attach -mountpoint $MOUNT_POINT $DISK_IMG
+else
+    mkdir -p $MOUNT_POINT
+    sudo mount -o loop $DISK_IMG $MOUNT_POINT
+fi
```

patchコマンドを実行してパッチを適用する
```sh
$ patch -p1 < mac.patch
```
とのことだったが, 実行すると
```sh
patch: **** out of memory
```
となった. そのため, 諦めて手作業でパッチを当てた.



### QUEMのインストール
```sh
$ brew install qemu
```


### LLVMのインストール
```sh
$ brew install llvm@9
$ export PATH=/usr/local/opt/llvm@9/bin:$PATH (.zshrcにも書き込む)
```
インストールが上手くいかなかった. 先に
```sh
$ xcode-select --install
```
を行うとインストールすることができた.



### その他のビルドに必要なパッケージのインストール
```sh
$ brew install nasm dosfstools binutils
$ export PATH=/usr/local/opt/binutils/bin:$PATH
```


### バイナリエディタ「0xED」のインストール
```sh
$ brew install 0xed
```
実行すると, 「そんなのないよ」と言われたので, 代わりにApp Storeから `Hex Fiend` をインストール. 


***
- [1章 PCの仕組みとハローワールド](./section1)
- [2章 EDK II入門とメモリマップ](./section2)
- [3章 画面表示の練習とブートローダ](./section3)


