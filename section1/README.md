
# 1章
## Hello, World!

### ダウンロード
mikanos-build リポジトリから
- `day01/bin/hello.efi`(バイナリコード)
- `devenv/OVMF_CODE.fd`
- `devenv/OVMF_VARS.fd`  
を持ってくる.  
hello.efiを一部変更すれば'Hello, World!' 以外の文字を出力できる.  
UEFIブートするためには, OVMFファイルというのが必要らしい(*.fdのやつ). 

```sh
$ curl -O https://raw.githubusercontent.com/uchan-nos/mikanos-build/master/day01/bin/hello.efi
$ curl -O https://raw.githubusercontent.com/uchan-nos/mikanos-build/master/devenv/OVMF_CODE.fd
$ curl -O https://raw.githubusercontent.com/uchan-nos/mikanos-build/master/devenv/OVMF_VARS.fd
```


### イメージを作ってFATでフォーマット
```sh
$ qemu-img create -f raw mikan.img 200M
$ mkfs.fat -n 'MIKAN_OS' -s 2 -f 2 -R 32 -F 32 mikan.img
```

### イメージをマウントして, バイナリファイルを書き込んで, アンマウント
```sh
$ hdiutil attach -mountpoint mnt mikan.img
$ mkdir -p mnt/EFI/BOOT
$ cp hello.efi mnt/EFI/BOOT/BOOTX64.EFI
$ hdiutil detach mnt
```


### バイナリファイルを書き込んだイメージをQEMUで読み込み, 起動する
```sh
$ qemu-system-x86_64 -drive if=pflash,file=OVMF_CODE.fd -drive if=pflash,file=OVMF_VARS.fd -hda mikan.img
```

![Hello World !](./image/hello_yuta.png)


実行するとQEMUにカーソルを奪われるが, Macなら `Control + Option + g` でカーソルが戻ってくる. おかえり. 



### 自動化
[karaage0703さん](https://zenn.dev/karaage0703/scraps/b2705131673377)がMac用にパッチを当てたリポジトリを用意してくれている. (patchコマンドを実行したのなら, このgit clone は必要ないかも)
```sh
$ git clone -b karaage https://github.com/karaage0703/mikanos-build osbook
```

以下のスクリプト実行すると, ここまでの作業を実行してくれる. これからはこれを使おう. 
```sh
$ ~/osbook/devenv/run_qemu.sh hello.efi
```



