# Hello world
マックでhello.efi(BOOTX64.EFI)を実行するまでの手順が、ネットに拡散している情報が古かったりで動かないので、ここに残す

## 前提として
- バイナリはカキコ終えてる
- 必要な２種類のファイルはDLずみ
  - curl -O https://raw.githubusercontent.com/uchan-nos/mikanos-build/master/devenv/OVMF_VARS.fd
  - curl -O https://raw.githubusercontent.com/uchan-nos/mikanos-build/master/devenv/OVMF_CODE.fd


>
ネットで転がってるhdiutil attach --mounthpathは動くことないので注意
ここで行いたいのは、qemu-imgで作った仮想imgを何処でもいいからマウントして、
そのディレクトリにBOOTX64.EFIファイルを保存することが目的なので、それさえ満たせれば手順は何でもいい。


ディレクトリ構成はこんな感じ
```
. myos/
├── myos.img
├── OVMF_VARS.fd
└── OVMF_CODE.fd
```



```terminal
$ qemu-img create myos.img 
$ hdiutil attach myos.img
$ mkdir -p /Volumes/MY\ OS/EFI/BOOT
$ mv hello.efi /Volumes/MY\ OS/EFI/BOOT/BOOTX64.EFI
$ hdiutil detach disk3 # 普通にejectでもいい

$ qemu-system-x86_64 \
  -drive if=pflash,file=$PWD/OVMF_CODE.fd \
  -drive if=pflash,file=$PWD/OVMF_VARS.fd \
  -hda ./myos.img
```

上記の説明としては、最初にqemu-imgで仮想イメージを作成後に、macについてるhdiutiを利用して、/Volumesにマウントしている。面倒だったらダブルクリックでマウントでもいい。
上記で作成した仮想イメージに、EFI/BOOTというディレクトリを作成する。ここがqemu実行時というかブートローダー？の実行時に自動で参照され実行されるバイナリの保存場所になる。
ディレクトリが作成されたらBOOTX64.EFIを作成したディレクトリに保存して、qemuを実行すればhello world!

ちなみに、どのディスクにマウントされてるかいい感じにみたい場合は
```terminal
$ diskutil list 
```

