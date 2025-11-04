このファームウェアは試験的バージョンです。

MSX-AUDIOのファームウェアです。
I2S出力版なので別途、PCM5102等のI2S対応DACモジュールが必要です。

音声データの生成にemu8950(great!)を使用しています。
https://github.com/digital-sound-antiques/emu8950

パナソニックFS-CA1との違い

1. Y8950の音声出力機能以外は一切機能しません。
2. FS-CA1内蔵ソフトウェアは起動出来ません。内蔵データも利用できません。
3. 外部RAM=256Kbyte、外部ROM=0Kbyteです。(外部記憶装置はRAM固定です)
4. ステータス/データのread時にwaitがかかります。

結線方法

MSXπ側  DACモジュール側
VCC     VIN
GND     GND
IO32    LRCK
IO34    DIN
IO33    BCK
IO35    SCK(不必要な配線なので繋がなくても構いません)

音声フォーマットは16ビット、モノラル2CH、サンプルレート49716Hzです。

御注意
MSXπ✨✨✨✨✨✨✨に配線を追加する必要があります。mod.jpgの通りに配線して下さい。
配線を行なわないと割り込み信号を使用したプログラムが正常動作しません。
basicでcall audioする場合以外は配線無しでも概ね大丈夫っぽいです。

使用法

1. audio_i2s.binファイルの後ろにFS-CA1のROMファイル(fs-ca1_CLEAN.rom)を連結します。

unixのcatコマンドを使用する例
$ cat audio_i2s.bin fs-ca1_CLEAN.rom > firmware.bin

windowsのcopyコマンドを使用する例
>copy /b audio_i2s.bin + fs-ca1_CLEAN.rom firmware.bin

2. uf2conv.exeを用いて1で作成したfirmware.binをuf2ファイルに変換します。

例: windowsコマンドプロンプト上で
uf2conv.exe firmware.bin firmware.uf2

uf2conv.exeはhttps://github.com/piigaa-densetu-two-dai/MSXpi_plus_plus_plus_plus_plus_plus_plusにあります。

3. MSXに刺さっていない状態のMSXπ✨✨✨✨✨✨✨をブートモードでPCに接続します。

MSXπ✨✨✨✨✨✨✨のBOOTSELボタンを押しながらPCとUSB接続して下さい。
接続が成功するとドライブが認識されます。

4. MSXπ✨✨✨✨✨✨✨に2で作成したfirmware.uf2ファイルを書き込みします。

3で認識されたドライブにfirmware.uf2をドラッグアンドドロップ(コピー)します。
コピーが完了するとドライブが見えなくなります。

5. 書込みが終わったらPCから外します。

メモリマップドIOポート仕様

0x3fff(write) bit7-bit1: 未使用 bit0: 0(初期値)=後述IOポート無効 1=後述IOポート有効
0x7fff(write) 上記のミラー

IOポート仕様

0xc0 (write) アドレス書込み
0xc1 (write) データ書き込み
0xc0 (read) ステータス読み込み
0xc1 (read) データ読み込み(常に0x00を返す)
