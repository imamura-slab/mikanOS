

# 3章
## QEMUモニタ

CPUのレジスタ値を確認する
```
(qemu) info registers
RAX=0000000000000000 RBX=0000000000000001 RCX=000000003fb7b1c0 RDX=0000000000000031
RSI=0000000000000400 RDI=000000003fea92d0 RBP=000000000000002e RSP=000000003fea88a0
R8 =0000000000000000 R9 =000000003fecc30f R10=0000000000000050 R11=0000000000000000
R12=000000003e6965c0 R13=000000003fea8930 R14=000000003f226920 R15=000000003fea88e8
RIP=000000003e69651b RFL=00000206 [-----P-] CPL=0 II=0 A20=1 SMM=0 HLT=0
ES =0030 0000000000000000 ffffffff 00cf9300 DPL=0 DS   [-WA]
                      ...
                      ...
                      ...
```

指定したアドレス付近の値を表示（メモリダンプ）する. おそらく環境の違いによるものだと思うが, 本に載っていたアドレスをそのまま使うと上手くいかなかった. レジスタ値確認のときに出てきた `RIP` の値を使うことで上手くいった...? 
```
(qemu) x /4xb 0x03e69651b
000000003e69651b: 0xeb 0xfe 0x48 0x83
```

jmp命令は本と同じだが, その後の命令が本とは違うものになっている. (本では `xchg %ax, %ax`)
```
(qemu) x /2i 0x03e69651b
0x3e69651b:  eb fe                    jmp      0x3e69651b
0x3e69651d:  48 83 ec 28              subq     $0x28, %rsp
```


