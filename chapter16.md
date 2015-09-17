## 第16章　リストの操作

この章は、scalaのデータ構造の１つであるList（リスト）についての説明である。

### 16.1 リストリテラル
リストは'a','b','c'を要素とするリストはList('a','b','c')で表され、配列の違いとしてリストはイミュータブル（値の変更ができない、要素の追加ができない）であり、再帰的な構造を持つ。

```scala
val diag3:List[List[Int]] = List(
			 List(1,0,0),
			 List(0,1,0),
			 List(0,0,1))
```

再帰的な構造の例としては、以下の階乗を求める例のように、関数の中で関数を呼び出している状況を指す。

```scala
def Kaijyou(n: Int): Int = n match {
	case 0 => 1
	case n => n * Kaijyou(n - 1)
}
```


### 16.2 List型
配列と同様に、リストの中の要素はすべて同じ型になっている。T型の要素を集めたリストの型はList[T]となる。また、リスト型は共変であり、S型がT型のサブ型ならばList[S]はList[T]のサブ型である。例をあげれば、List[String]はList[Object]のサブ型である。

### 16.3 リストの構築
リストはNilと::(コンス)の２つから組み立てられる。Nilは空リストを指し、::はx :: xs :: NilとすることでList(x,xs)と同等の意味になる。

### 16.4 リストに対する基本操作
リストは以下の３つの操作によって表現される。
1. head:リストの先頭要素を返す
2. tail:先頭要素を除くすべての要素から構成されるリストを返す
3. isEmpty:リストがからのときtrueを返す。

### 16.5 リストパターン
リストはパターンマッチで分解することができる。List(...)という形式のパターンを使用すれば、リストのすべての要素をマッチさせることができる。

```scala
val fruit = List("apples", "oranges", "pears")
val List(a,b,c) = fruit
//結果
a: String = apples
b: String = oranges
c: String = pears

val a :: b :: rest = fruit
//結果
a: String = apples
b: String = oranges
rest: List[String] = List(pears)
```

１つ目の例では、リストの長さが３のリストにマッチし、パターン変数a,b,cに３つの要素を束縛する。リストの長さがあらかじめわからない場合は::を使ってマッチさせる。２つ目の例では、長さ２以上のリストにマッチする。  
ここでの疑問点は、a,b,restの３つの変数を与えたときに、なぜrestのみList型になるのかという点である。  
これは、::が最後の文字がコロンなので右結合となるからA :: B :: C はA :: (B :: C)となるため、a, bは文字列として返されるが、残りの１つはリストとなるからである。

###16.6 Listクラスの一階メソッド
この節では、リスト操作プログラムの構成テクニックとして、パラメータとして関数をとらないメソッドである一階メソッドの説明をする。

#### 16.6.1 ２個のリストの連結
リストの連結は::とよく似た操作で、:::と書かれる。:::は被演算子として２個のリストをとる。xs ::: ysの結果値は、xsのすべての要素の後ろにysのすべての要素が続く形となる。

#### 16.6.2 分割統治原則
連結メソッドであるappendはパラメータとして、連結される２つのリストをとる、これらのリストは要素型が一致していなければならないが、要素自体はなんでもよい。これは、appendに入力リストの要素型を示す型パラメータを与えれば表現できる。

```scala
def append[T](xs: List[T], ys: List[T]):List[T]
```
appendに実装を設計するにあたり、リストのような再帰的な構造を操作するための分割統治原則を思い出す必要がある。リストを対象とする多くのアルゴリズムはパターンマッチを使って最初に入力リストを単純なケースに分割する。これが、原則の中の分割である。つぎに、個々のケースのための結果値を作る。結果値が空でないリストなら、その一部は同じアルゴリズムを再帰的に呼び出すことによって組み立てる。これが統治にあたる。

#### 16.6.3　リストの長さを計算する：length
lengthメソッドは、リストの長さを計算する。

```scala
List(1,2,3).length
//実行結果
res3: Int=3
```
リストの長さ計算は、配列と異なり、比較的コストのかかる操作となっている。そのためxs.isEmptyのような操作をxs.length == 0に置き換えるの好ましくない。

#### 16.6.4 リストの末尾へのアクセス：initとlast
lastは（空でない）リストの最後の要素を返し、initは最後の要素を除く部分からなるリストを返す。

```scala
val abcde = List('a','b','c','d','e')

abcde.last
res4:Char = e

abcde.init
res5:List[Char] = List(a,b,c,d)
```
この２つの関数も、headやtailと同じように、空のリストに適用すればエラーを返す。

```scala
List().init
java.lang.UnsupportedOperationException: Nil.init

List().last
java.until.NoSuchElementException: Nil.last
```
headやtailは、ともに一定の時間で実行されるが、initとlastはリスト全体をたどらないと結果値を計算できないので、リストの長さに比例する計算時間を要する。

#### 16.6.5 リストの反転：reverse
リストの末尾へのアクセスが頻繁に行われれる場合には、先にリストを反転して、先頭の要素にアクセスすれば計算時間の短縮になる。リストの反転は以下のようにする。

```scala
abcde.reverse
res6: List[Char] = List(e,d,c,b,a)

```
reverseメソッドは他のリスト操作と同様に、操作対象のリストを書き換えるのではなく新しいリストを作る。
リストはイミュータブルなので、変更は不可能である。

#### 16.6.6 プレフィックスとサフィックス:drop,take,splitAt
dropとtakeは、リストの先頭と末尾から任意の要素数のリストを返すものである。
splitAtは指定された添え字の位置でリストを分割し、２個のリストのペアを返す。

```scala
abcde take 2
res8: List[Char] = List(a,b)

abcde drop 2
res9: List[Char] = List(c,d,e)

abcde splitAt 2
res10: (List[Char],List[Char]) = (List(a,b),List(c,d,e))
```

#### 16.6.7 要素の選択：applyとindices
ランダムな要素のアクセス方法としてapplyメソッドがありますが、リストではこの操作はあまり使用されない。

```scala
abcde apply 2
res11: Char = c

abcde(2)//applyが省略されている
res12: Char = c
```
indicesメソッドでは、リスト内の有効なすべての添字から構成されるリストを返す。

```scala
abcde.indices
res13: scala.collection.immutable.Range = Range(0,1,2,3,4)
```

#### 16.6.8 リストのリストから単層のリストへ：flatten
flattenメソッドは複数のリストを引数にとり、それらを１つのリストに平坦化する。

```scala
List(List(1,2),List(3),List(),List(4,5)).flatten
res14:List[Int] = List(1,2,3,4,5)
```

#### 16.6.9 リストのジッパー操作：zipとunzip
zipは２個のリストから、ペアのリストを１つ作る。zipWithIndexでは自動でインデックスをつけてくれる。

```scala
abcde.indices zip abcde
res17: scala.collection.immutable.IndexedSeq[(Int, Char)] = IndexedSeq((0,a),(1,b),(2,c),(3,d),(4,e))

//上と同じ操作になる
abcde.zipWithIndex
res18: scala.collection.immutable.IndexedSeq[(Int, Char)] = IndexedSeq((0,a),(1,b),(2,c),(3,d),(4,e))

```
引数となるリストの長さが異なる場合は、短い方にあわせられ、相手のない要素は捨てられる。
unzipメソッドでは、タプルのリスト（複数組のリスト）をリストのタプル（リストの複数組）に変えられる。

```scala
val zipped 0 abcde zip List(1,2,3)
zipped: List[(Char, Int)] = List((a,1),(b,2),(c,3))

zipped.unzip
res19: (List[Char], List[Int]) = (List(a,b,c),List(1,23))
```

#### 16.6.10 リストの表示：toStringとmkString
toStringは、リストの標準的な文字列表現を返す。toStringとは別の表現で欲しい場合は、xs mkString(pre, sep, post)を使うことで実現できます。

```scala
abcde mkStrig ("[", ",", "]")
res21:Strign = [a,b,c,d,e]
```
mkStringを使えば、データをcsv形式で表現するときに、便利そうですね。

#### 16.6.11 リストの変換：iterator,toArray,copyToArray
Array型とList型の変換には、toArrayとtoListを使えば変換できます。 
また、指定した場所にArrayの要素をコピーしたい場合は、copyToArrayが使えます。

```scala
val arr2 = new Array[Int](10)
List(1,2,3) copyToArray(arr2, 3)
arr2
//結果
res28:Array[Int] = Array(0,0,0,1,2,3,0,0,0,0)
```
これは、arr2の３番目の要素からスタートしてList(1,2,3)をコピーするというコードになります。  
iteratorメソッドを使用すればリストの１つ１つの要素にアクセスできます。

```scala
val it = abcde.iterator
it.next
res29: Char = a
it.next
res30: Char = b
```

#### 16.6.12 サンプル：マージソート
省略

### 16.7 Listクラスの高階メソッド
リストを対象とした演算で、すべての要素をなんらか方法で変換するなどをする場合、Javaではfor,whileを使用し表現されていた。scalaではListクラスのメソッドとして実装されている高階演算子を使用し簡潔に表すことができる。

#### 16.7.1 リストの各要素のマッピング（変換）：map,flatMap,foreach
演算xs map fは、被演算子としてList[T]型のリストxs、T => U型の関数fをとり、xsの個々の要素に関数fを適用した結果値を集めたリストを返す。

```scala
List(1,2,3) map (_ + 1)
res32:List[Int] = List(2,3,4)

val words = List("the", "quick", "brown", "fox")
words map (_.length)
res33:List[Int] = List(3,5,5,3)
```

flatMapはmapと似ているるが、右被演算子として要素のリストを返す関数をとる。リストの各要素に関数を適用し、結果値を連結したリストを返す。

```scala
words map (_.toList)
res35: List[List(Char)] = List(List(t,h,e),List(q,u,i,c,k),List(b,r,o,w,n),List(f,o,x))

words flatMap (_.toList)
res36: List[Char] = List(t,h,e,q,u,i,c,k,b,r,o,w,n,f,o,x)
```
mapは複数のリストからなるリストを返しているのに対し、flatMapは要素のリストをすべて結合した単一のリストを返している。 
1<=j<i<5になるすべての(i,j)の対のリストを作ると

```scala
List.range(1,5) flatMap (i => List.ranga(1,i) map (j => (i, j)))
res37:List[(Int,Int)] = List((2,1), (3,1),(3,2),(4,1),(4,2),(4,3))
```
List.rangeは、一定範囲に含まれるすべての整数のリストを作るメソッドである。  
foreachは右被演算子として手続き（結果型がUnitの関数）をとる。Unit型であるため、結果のリストを作らない。

```scala
var sum = 0
List(1,2,3,4,5) foreach (sum += _)
sum
res39: Int = 15
```

#### 16.7.2 リストのフィルタリング：filter,partition,find,takeWhile,dropWhile,span
xs filter pは被演算子として、List[T]型のリストxs、T => Boolean型の述語関数pをとり、結果値としてxsの中でp(x)がtrueになるすべての要素xを集めたリストを返す。

```scala
List(1,2,3,4,5) filter (_ % 2 == 0)
res40:List[Int] = List(2,4)
```
ここで、感じたのが現在すべての要素を表すプレースフォルダーだが、その場所を演算で使えるのかということだ。
例えば、List(1,2,3,4,5)の各要素に１を加算して、２で割り切れるもののもとの数字をフィルタする。という命題があったとしよう。
（単純に、奇数をとってくればよいのだが（笑））
はじめに考えた手法としては

```scala
List(1,2,3,4,5) filter ((_ + 1) % 2 == 0)
<console>error: missing parameter type for expanded	
		function((x$1) => x$1.$plus(1))
		List(1,2,3,4,5) filter((_ + 1) % 2 == 0)
								^
```
という感じに怒られてしまった。  
結果的にどうすればよかったのかというと、ヒントは「List[T]型のリストxs、T => Boolean型の述語関数pをとる」ここにありました。つまり、filterの右では関数をとり、その結果がBoolean型になればよいので、直接、関数を書き込めばいいんです。

```scala
List(1,2,3,4,5) filter ((i:Int) => (i + 1) % 2 == 0)
res41: List[Int] = List(1,3,5)	
```
こんな感じで、ちゃんと返してくれました。  

続いて、partitionメソッドは、filterとにているが、リストのペアを返すものである。片方のリストには述語関数がtrueになるものを格納しており、もう一方はfalseになるものを格納する。

```scala
List(1,2,3,4,5) partition (_ % 2 == 0)
res42: List(List[Int],List[Int]) = (List(2,4), List(1,3,5))
```

findメソッドも述語関数がtrueとなるものを返すが、filterとpartitionと違うのは、要素のリストではなく、最初の要素を返す。注意する点としては、返されるのはOption値である。

```scala
List(1,2,3,4,5) find (_ % 2 == 0)
res43: Option[Int] = Some(2)

List(1,2,3,4,5) find (_ <= 0)
res44: Option[Int] = None
```

takeWhileとdropWhileも、右被演算子として述語関数をとる。xs takeWhile pは、リストxsの先頭からpを成功させる要素の連続を取れるだけ取って返す。逆に、dropWhileはリストの先頭から成功させる要素の連続を削除して、残りのリストを返す。

```scala
List(1,2,3,-4,5) takeWhile(_ > 0)
res45:List[Int] = List(1,2,3)

List("the","quick","brown","fox") dropWhile (_.startsWith "t")
res46:List[java.lang.String] = List(quick, brown, fox)
```
spanメソッドは、takeWhileとdropWhileを１つにまとめたものである

```scala
List(1,2,3,-4,5) span(_ > 0)
res47: (List[Int],List[Int]) = (List(1,2,3),List(-4,5))
```

#### 16.7.3 リストを対象とする述語関数:forallとexists
forallはリストのすべての要素が述語関数を満足するとき結果値がtrueになる。一方、existsは、述語関数を満足させる要素がある場合にtrueを返す。以下の例は、与えられるリストのすべての要素が0の場合をチェックする関数である。

```scala
def hasZeroRow(m: List[List(Int)]) =
	m exists (row => row forall (_ == 0))

val diag3 =
	List(
		List(1,0,0),
		List(0,1,0),
		List(0,0,1)
		)

hasZeroRow(diag3)
res48: Boolean = false
```

#### 16.7.4 リストの畳み込み:　/:と:\
リストの畳み込みの演算として、左畳み込み/:と右畳み込み:\が存在する。 
例えば、List(1,2,3)のすべての和を左畳み込みで表現すると、0 + 1 + 2 + 3は

```scala
def sum(xs : List[Int]):Int = (0 /: xs) (_ + _)
```

と定義できる。(z /: xs) (op)は、先頭値のz、リストのxs、二項演算子のopという３つのオブジェクトからなる。結果値は、zを先頭におき、リストの要素を並べて、その間に演算opを適用した値である。その逆の順に処理を行うのが右畳み込みである。左畳み込みと異なる点は、３つの演算子のうち、はじめの２つが逆に現れるところである。

#### 16.7.5 foldを使ったリストの反転
よくわからなかったので割愛！

#### 16.7.6 リストのソート:sortWith
リストのソートはsortWithメソッドで実現できる。

```scala
List(1,-3,4,2,6) sortWith(_<_)
res51:List[Int] = List(-3,1,2,4,6)
```

### 16.8 Listオブジェクトのメソッド
これまでの操作は、すべてListクラスのメソッドとして実装されているので、リストオブジェクトをレシーバとして呼び出す。
それに対し、Listクラスのコンパニオンオブジェクトであり、グローバルにアクセスできるscala.Listオブジェクトにもメソッドが含まれている。これらの一部のリストを作るファクトリーメソッドである。特定の形のリストを対象として使われるものもある、この節では、これら２つのタイプのメソッドを示す。

####16.8.1 要素からリストを作る：List.apply
List(1,2,3)とあるがこれは、List.apply(1,2,3)と同じ意味となる

#### 16.8.2 数値の範囲を作る：List.range
rangeメソッドは、一定範囲の数値から構成されるリストを作るものである。引数としては、List.range(form,until,step)となり開始・終了・加減具合というふうになる。

```scala
List.range(1,5)
res54:List[Int] = List(1,2,3,4)

List.range(1,9,2)
res55:List[Int] = List(1,3,5,7)

List.range(9,1,3)
res56:List[Int] = List(9,6,3)

```

####16.8.3 同じ値のリストを作る：List.fill
fillメソッドは、０個以上の同じ要素からリストを作る。引数は、作成するリストの長さとリストに格納する要素の２つである。

```scala
List.fill(5)('a')
res57:List[Char] = List(a,a,a,a,a)

List.fill(3)("hello")
res58:List[java.lang.String] = List(hello,hello,hello)

```
fillに３個以上の引数を渡すと、多次元リストが作成される。（例：リストのリスト、リストのリストのリスト）

```scala
List.fill(2,3)('b')
res59:List[List[Char]] = List(List(b,b,b),List(b,b,b))
```





























