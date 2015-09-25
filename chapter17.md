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
PredefがStringからStringOpsへの暗黙の型変換を提供しているので、すべての文字列はシーケンスのように扱える。

```scala
def hasUpperCase(s:String) = s.exists(_.isUpper)

hasUpperCase("Robert Frost")
res14:Boolean = false
```


###17.2 集合（Set）とマップ
SetやMapと単に書くと、デフォルトではイミュータブルなオブジェクトが作られる。ミュータブルなものが必要なら、明示的にインポートが必要となる。

####17.2.1　集合の使い方
集合のもっとも重要な特徴として、==で比較して同じとされる要素が１つしかない（つまり重複する要素がない）ということである。そのため、集合を用いると、文字列に含まれる単語が何個あるのかを数えることが可能になる。

```scala
val text = "See Spot run. Run, Spot. Run!"

val wordsArray = text.split("[!,.]")
wordArray
res15: Array(See, Spot, run, Run, Spot, Run)

```
単語の種類を数えるには、大文字と小文字を統一して、ミュータブルな集合に追加する。

```scala
for(word <- wordsArray)
		words += word.toLowerCase
words
res17:Set[String]=Set(spot, run, see)
```

####17.2.2 マップの使い方
マップを使えば、値とコレクションの中の各要素を対応付けられる。マップは数字の添え字ではなく、任意の種類のキーを使えるはいれつのようなものである。  
マップを作成するときには、２つの型を定義することに注意しなければならない。マップのキー(key)の型と値(value)の型である。

```scala
val map = mutable.Map.empty[String, Int]
map("hello") = 1
map("there") = 2
map
res21:Map[String,Int] = Map(hello -> 1, there ->2)

map("hello")
res22:Int = 1

```


####17.2.3 デフォルトの集合とマップ
ほとんどの用途において、ファクトリーが提供するミュータブルあるいはイミュータブルな集合やマップの実装を使えば十分である。内部の処理において、最適な処理を行うようにしており処理スピードが高速になるようにしている。

####17.2.4 ソートされた集合とマップ
イテレーターが特定の順序で要素を返すような集合やマップが必要となることがある。そのために、SortedSet,SortedMapというトレイトを提供している。これらのトレイトは、要素の順序を管理するTreeSetクラスや、キーの順序を管理するTreeMapクラスによって実装されている。要素の順序は、Orderedトレイトによって定義されるので、集合の要素型やマップのキー型は、このトレイトをミックスインするか、暗黙のうちに型変換可能なものにしなければならない。

```scala
//TreeSetの例
import scala.collection.immutable.TreeSet
val ts = TreeSet(9,3,1,8,0,2,7,4,6,5)
TreeSet[Int] = TreeSet(0,1,2,3,4,5,6,7,8,9)

val cs = TreeSet('f','u', 'n')
TreeSet[Char] = TreeSet(f, n, u)

//TreeMapの例
import scala.collection.immutable.TreeMap
var tm = TreeMap(3 ->'x',1 ->'x',4 ->'x')
TreeMap[Int,Char] = Map(1 -> x , 3 -> x, 4 -> x)

tm += (2 -> 'x')
tm
res30:TreeMap[Int,Char] = Map(1 -> x ,2 -> x 3 -> x, 4 -> x)
```

###17.3 ミュータブルとイミュータブルどちらを使うべきか
ミュータブルかイミュータブルなコレクションのどちらを使うかは、問題によって異なる。わからない場合は、イミュータブル版の方が動作の想定をしやすい。なので、はじめはイミュータブルで実装しておいて、後でミュータブルなものに置き換えるなどすればよい。

###17.4 コレクションの初期化
コレクションを作成・初期化するもっとも一般的な方法は、コレクションのコンパニオンオブジェクトのファクトリーメソッドに初期要素を渡すというものである。コンパニオンオブジェクト名の後ろに括弧をつけ、その中に要素を書き込めば、コンパイラがそれをコンパニオンオブジェクトのapplyメソッド呼び出しに変換する。

```scala
val colors = List("blue","yellow","red","green")
import scala.collection.immutable.TreeSet
val treeSet = TreeSet[String]() ++ colors
TreeSet[String] = TreeSet(blue, green, red, yellow)

```
上の例では、空のTreeSet[String]を作り、TreeSetの++演算子を使用してリストを追加している。

####17.4.1 配列やリストの変換
他のコレクションを使って、リストや配列を初期化するのは非常に簡単である。他のコレクションを使って新しいリストを初期化するには、単純にコレクションのtoListやtoArrayを呼び出せばよい。

```scala
treeSet.toList
res50: List[Strign] = List(blue, green, red, yellow)

treeSet.toArray
res51: Array[Strign] = Array(blue, green, red, yellow)
```
元のcolorsリストではソートされていないのに、TreeSetではアルファベット順にソートされているのに注意する。


####17.4.2 集合・マップのミュータブル版とイミュータブル版の相互変換















