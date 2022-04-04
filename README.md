# DM16

SwissMicros Model DM16L RPN Calculator

https://www.swissmicros.com/product/dm16l


GSB [Label]

return X


[Label:0]

Power multiplier: Y^x

base on Y, exponent on X.

[Label:1]

permutation: xPy

Y is the rank (starting value), X is the number of operations.

[Label:2]

radian transformation(degree>radian):

X is degree [DEG], if x is 0, then y is angle [RAD].

PI=355/113=3.14159292

[Label:3]

Cosine: y is the angle [DEG], X is the number of operations in the Taylor expansion

sinx=±( (1-(cosx)^2)[root] )

tanx=sinx/cosx


入力開始: g P/R

入力終了: g P/R

関数呼び出し: GSB [Label名]

一行目関数呼び出し準備: GTO .000

一行目関数呼び出し準備(計算状態で): f PRGM

実行: R/S

70行までなら記憶メモリ領域16個(0~f)を維持

GSBを使うときはxとyに必ず引数を入れて呼ぶ

戻りはxに入って、zだったものがyに入ってるというルール


メモ:

入力値の繰り返し演算は[ENTER]3回


●(GTO F) プッシュ用ラベル ZとIを保存

メモリ 4,5 を破壊！

001: g LBL F	: 43,22, F	// プッシュ用ラベル

002: Rv	: 33	// y|z|t|x

003: Rv	: 33	// z|t|x|y

004: STO 4	: 44 4	// 4=z 

005: Rv	: 33	// t=z t|x|y|z

006: ACL I	: 45 32	// zを破壊 I|t|x|y

007: STO 5	: 44 5	// 5=I

008: Rv	: 33	// t|x|y|I

009: ACL 4	: 45 4	// Iを破壊 z|t|x|y

010: Rv	: 33	// t|x|y|z

011: Rv	: 33	// x|y|z|t

012: g RTN	: 43 21	// 





●(GTO E) ポップ用ラベル: ZとIを書き戻す

メモリ 4,5 を参照！

xを戻り値として維持し、yにプッシュ時のzを入れて返す

013: g LBL E	: 43,22, E	// ポップ用ラベル

014: ACL 5	: 45 5	// tを破壊 I|x|y|z

015: STO I	: 44 32	// I=5

016: Rv	: 33	// x|y|z|I

017: ACL 4	: 45 4	// Iを破壊 z!|x|y|z

018: x><y	: 34	// x|z!|y|z

019: g RTN	: 43 21	// 




●(GTO D) 1 return エラーリターン用ラベル スタック調整ロールダウン有り 

020: g LBL D	: 43,22, D	// エラー用ラベル

021: Rv	: 33	// y|z|t|x

022: Rv	: 33	// z|t|x|y

023: 1	: 1	// yを破壊 1|z|t|x|

024: g RTN	: 43 21	// 




●(GSB 0) べき乗: Yに底, Xに指数, Y^x。

定義として、x=0のとき1

025: g LBL 0	: 43,22, 0	// GSB呼び出しラベル

026: GSB F	: 21 F	// プッシュ

027: g X=0	: 43 40	// 引数チェック

028: GTO D	: 22 D	// 

029: STO I	: 44 32	// 

030: Rv	: 33	// 

031: STO 0	: 44 0	// 

032: 1	: 1	// 

033: g LBL 6	: 43,22, 6	// ループ用ラベル

034: ACL 0	: 45 0	// 

035: *	: 20	// 


036: g DSZ	: 43 23	// 

037: GTO 6	: 22 6	// 

038: GSB E	: 21 E	// ポップ

039: g RTN	: 43 21	// 





●(GSB 1) 順列:  Yは位数(開始値), Xは演算個数, xPy。

階乗はXとYを同じにすればいい

定義として、X=0のとき1,Y=0のとき1

Xレジスタ | Yレジスタ (| Zレジスタ ): Iレジスタ | メモリーアドレス0

040: g LBL 1	: 43,22, 1	// [N|X:|] GSB呼び出しラベル

041: GSB F	: 21 F	// プッシュ

042: g X=0	: 43 40	// 引数チェック

043: GTO D	: 22 D	// 

044: g X>Y	: 43 3	// 引数チェック

045: GTO D	: 22 D	// 

046: STO I	: 44 32	// [N|X:N|]

047: Rv	: 33	// [X|:N|]

048: STO 0	: 44 0	// [X|:N|X]

049: g LBL 7	: 43,22, 7	// [A|:N|X] ループ用ラベル

050: g DSZ	: 43 23	// [A|:(N-1)|X]

051: GTO 8	: 22 8	// [A|:N|X]

052: GSB E	: 21 E	// ポップ

053: g RTN	: 43 21	// [A|:|]

054: g LBL 8	: 43,22, 8	// [A|:N|X] メインルート分岐用ラベル

055: ACL 0	: 45 0	// [X|A:N|X]

056: ACL I	: 45 32	// [N|X (|A):N|X]

057: -	: 30	// [D(X-N)|A:N|X]

058: *	: 20	// [A|:N|X]

059: GTO 7	: 22 7	// [A|:N|X]




●(GSB 2) ラジアン(度[degree]>ラジアン(radian)変換):

Xは度[DEG], xが0だったらyが角度[RAD]

PI=22/7=355/113=3.14159265358979

[DEG]/(180/PI)=[RAD]

([DEG]/180)*PI=[RAD]

([DEG]/180)*22/7=[RAD]

[DEG]*22/1260=[RAD]

[DEG]*11/630=[RAD]

([DEG]/180)*355/113=[RAD]

[DEG]*355/20340=[RAD]

[DEG]*71/4068=[RAD]	// 有効桁数と必要行数のバランスでコレを採用

([DEG]/180)*3.141592654=[RAD]

[DEG]*3.141592654/180=[RAD]

[DEG]*0.01745=[RAD]

060: g LBL 2	: 43,22, 2	// GSB呼び出しラベル

061: GSB F	: 21 F	// プッシュ

062: f FLOAT 9	: 42,45, 9	// 

063: g X!=0	: 43 48	// 引数チェック

064: GTO 9	: 22 9	// 


065: x><y	: 34 // ポップでxが消えるように "roll down" でなく "exchange"

066: GTO A	: 22 A	// 


067: g LBL 9	: 43,22, 9	// メインルート分岐用ラベル

068: 7	: 7	// 

069: 1	: 1	// 

070: *	: 20	// *71

071: 4	: 4	// 

072: 0	: 0	// 

073: 6	: 6	// 

074: 8	: 8	// 

075: /	: 10	// /4068


076: g LBL A	: 43,22, A	// 終了処理用ラベル

077: GSB E	: 21 E	// ポップ

078: g RTN	: 43 21	// 





●(GSB 3) コサイン: yは角度[DEG]、Xはテイラー展開の演算個数

テイラー展開（マクローリン級数）

メモリが厳しいこともあり、CORDICは使えない

GSBするのでプッシュポップを呼んでも復帰できない

GSBするのでメモリ0,4,5は使えない、メモリ1,2,3を使用

sinx=±( (1-(cosx)^2)(ルート) )

tanx=sinx/cosx

079: g LBL 3	: 43,22, 3	// GSB呼び出しラベル

080: g X=0	: 43 40	// 引数チェック

081: GTO D	: 22 D	// 

082: STO I	: 44 32	// n

083: Rv	: 33	// 

084: GSB 2	: 21 2	// 

085: STO 1	: 44 1	// x

086: 1	: 1	// 

087: STO 2	: 44 2	// Ans

088: g LBL B	: 43,22, B	// ループ用ラベル

089: 1	: 1	// 

090: CHS	: 49	// -1

091: ACL I	: 45 32	// n

092: GSB 0	: 21 0	// (-1)^n


093: ACL 1	: 45 1	// x

094: 2	: 2	// 

095: ACL I	: 45 32	// n

096: *	: 20	// 2*n

097: STO 3	: 44 3	// 3=2n

098: GSB 0	: 21 0	// x^2n

099: *	: 20	// 2n

100: ACL 3	: 45 3	// 2n

101: ACL 3	: 45 3	// 2n

102: GSB 1	: 21 1	// P(2n,2n)=2n!

103: /	: 10	// 

104: ACL 2	: 45 2	// Ans

105: +	: 	// 40

106: STO 2	: 44 2	// Ans

107: g DSZ	: 43 23	// 

108: GTO B	: 22 B	// 

109: g RTN	: 43 21	// 最後のリターンは節約できる




