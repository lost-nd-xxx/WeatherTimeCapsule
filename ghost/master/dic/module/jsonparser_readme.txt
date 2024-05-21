
*************************************************************************
YAYA as SAORI　jsonパーサ with オブジェクト風の何か
*************************************************************************

jsonをjavascriptのオブジェクト風に読み込み、要素の取り出し等の操作が行えるYAYA as SAORIモジュールです。
基本的にAPIの簡単な利用が目的で複雑なオブジェクト操作はできません。
YAYA as SAORIで作っているのでSAORIとして機能を使う場合yaya.dll等と一緒に読み込ませる必要があります。
下記の手順で導入ができます。


(1)YAYA as SAORI本体ダウンロード
http://emily.shillest.net/ayaya/index.php?YAYA%20as%20SAORI
上のページから「YAYA as SAORI」本体をダウンロードして解凍します。

(2)YAYA as SAORI 本体のファイルをsaori用フォルダに入れる
解凍したフォルダのなかの「yaya_saori」フォルダ下の「ayaysaori.aym」、「builtins.aym」、「yaya.dll」、「yaya.txt」をsaori用のフォルダに入れてください。

(3)jsonパーサモジュールをsaori用フォルダに入れる
yaya_jsonRead.aymとyaya_iObj.aymをsaori用フォルダに入れてください。

(4)yaya.txtの書き換え
yaya.txtを書き換えて、yaya_jsonRead.aymとyaya_iObj.aymをYAYA as SAORIが読み込めるようにしてください。

「dic, yaya_jsonRead.aym」
「dic, yaya_iObj.aym」

と追記すれば良いです。

他に読み込むモジュールがなければこのモジュールに同梱しているyaya.txtで置き換えても大丈夫です。




*************************************************************************
＊外から呼びだす関数一覧
*************************************************************************

1. "JSON.readJson"
入力：json文字列　例："{"生徒":[{"出席番号":1,"名前":"山田太郎"},{"出席番号":2,"名前":"鈴木ひろし"}]}"
返り値：iオブジェクトID（ポインタのようなもの） 例:iObjxxx0

->json文字列を読み込んで、iオブジェクトに変換します。
返却されるiオブジェクトIDはそのあと、iオブジェクトを操作するのに使います。


2."IOBJ.iObj"
入力1：iオブジェクトID　
入力2以降：キー
返り値：iオブジェクトのキーの位置に対応する要素の値またはiオブジェクトID　
例：入力 "iObjxxx0","生徒",1,名前 => 返り値 鈴木ひろし

->iオブジェクトから要素や子のオブジェクトを取り出すのに使います。


3. "JSON.readFile"
入力1：ファイルパス　
入力2：文字コードID（任意）
返り値：テキスト

->ファイルのテキストを取得します。
文字コードIDが入力されない場合文字コードIDは1 (=UTF-8)と見なされます。
文字コードIDは「http://emily.shillest.net/ayaya/?マニュアル/文法/B.文字コード関連」に記載されています。

4."IOBJ.iObjToJson"
入力1:iオブジェクトID
入力2（任意）:フォーマットするなら1しないなら0、デフォルトで0
返り値：jsonオブジェクト文字列
iObjをjsonオブジェクト文字列に変換して返却します。
一部の表現を適切に返却できない場合があります。例えば指数はテキストとして返却されます。

5. "IOBJ.showIObj"
入力1:iオブジェクトID
返り値：フォーマットされたオブジェクト文字列

上のiObjToJsonの2つ目の引数が1の場合と同一の処理をします。インデントと改行をつけてフォーマットした文字列を返却します。

例：入力:iObjxxx0
出力：
{
 "生徒": {
   {
       "出席番号": 1,
       "名前": "山田太郎"
   },
   [
     "出席番号": 2,
     "名前": "鈴木ひろし"
   ]
 }
}




*************************************************************************
＊YAYAでの呼び出し例
*************************************************************************
「頭をクリックすると青空文庫の太宰の小説の作品名と情報を表示してくれる」です。

MouseDoubleClick_0Head
{
	aozoraSearch
}

aozoraSearch{
	'\![execute,http-get,https://api.bungomail.com/v0/books?作者名=/太宰/&limit=4&after=100-10,--async=OnHookAozora,--file=searchResult.json]'
}

OnHookAozora{
	_json=req.readFile(reference[3])
	LOGGING("json:"+_json)
	_jsonDatas=req.readJson(_json)
	_iObj=req.iObj(_jsonDatas,"books",0)
  	_txt="\_q"+req.iObj(_iObj,"作品名")+"\n"
	_txt+=req.showiObj(_iObj)+"\_q"
	_txt+"\e"
}

req.readJson{
	FUNCTIONEX('saori\yaya.dll', "JSON.readJson",_argv)
}
req.iObj{
	FUNCTIONEX('saori\yaya.dll', "IOBJ.iObj",_argv)
}
req.readFile{
	FUNCTIONEX('saori\yaya.dll', "JSON.readFile",_argv)
}
req.showiObj{
	FUNCTIONEX('saori\yaya.dll', "IOBJ.showIObj",_argv)
}


*************************************************************************
＊エスケープについて
*************************************************************************
さくらスクリプトで使う改行記号\nやタブ記号\tなどはそのままで、"\"を表す"\\”もそのままです。
一方でダブルコーテーション「"」を表す「\"」は「"」に直されています。


