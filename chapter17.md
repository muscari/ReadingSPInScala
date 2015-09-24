##第17章コレクション
この章では、よく使われるコレクション型と演算についての説明を行う。

###17.1 シーケンス
シーケンス型は、順序のあるデータを操作できるようにする。

####17.1.1 リスト
シーケンス型の１つとして、イミュータブルな連結リストを表現するListクラスがある。headメソッドやtailメソッドを使用するとリストの最初と最後の値にアクセスできる。

####17.1.2 配列
配列は要素のシーケンスを保持できる上に、要素の取得・変更のいずれの目的でも、０から始まる要素番号によってアクセスが可能。

```scala
val fiveInts = new Array[Int](5)
fiveInts: Array[Int] = Array(0,0,0,0,0)
fiveInts(0) = 4

fiveInts
res3:Array[Int] = Array(4,0,0,0,0)
```

####17.1.3 リストバッファー
ListBufferは、ミュータブルなオブジェクトであり、要素を追加する際のリスト構築の効率化を図ってくれる。良いところは、要素を先頭に挿入するとき+=:と、末尾に追加するとき+=も一定時間で処理できる。

```scala
import scala.collection.mutable.ListBuffer
val buf = new ListBuffer[Int]

buf += 1
res4:buf.type = ListBuffer(1)

buf += 2
res5:buf.type = ListBuffer(1,2)

3 +=: buf
res7:buf.type = ListBuffer(3,1,2)

buf.toList
List[Int] = List(3,1,2)

```
toListメソッドを使用すれば、Listが手に入る。

####17.1.4 配列バッファー
ArrayBufferは配列に似ているが、シーケンスの先頭と末尾で要素を追加・削除できるところが異なる。ArrayBufferを使うにはミュータブルコレクションパッケージからインポートする。また、型パラメータを指定しなければならないが、長さを指定する必要ななく、自動的に使用メモリのサイズを調節する。

```scala
import scala.collection.mutable.ArrayBuffer
val buf = new ArrayBuffer[Int]()

buf += 12
res9:buf.type = ArrayBuffer(12)

buf += 15 
res10:buf.type = ArrayBuffer(12,15)

buf.length
res12: Int = 2

buf(0)
res13: Int = 12
```

####17.1.5 文字列






















