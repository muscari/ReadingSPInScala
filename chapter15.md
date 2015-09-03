##第15章ケースクラスとパターンマッチ

この章では、通常のカプセル化されていないデータ構造を書くときに有用なケースクラス（case classes）とパターンマッチ(pattern matching)の構文について書かれている。

###15.1単純なサンプル
変数、数値、単項・二項演算を定義する。

```scala
abstract class Expr
case class Var(name: String) extends Expr
case class Number(num: Dobule) extends Expr
case class UnOp(operator : Strign, arg : EXpr) extends Expr
case class BinOp(operator: String, ldfr:Expr, right:Expr) extends Expr
```

####15.1.1ケースクラス
各サブクラスの前に”case”をつけることで、クラスケースを定義できる。  
○クラスケースの４つの利点

1. コンパイル時にクラスと同じ名前のファクトリーメソッドを追加する。  
	・インスタンスを作成するときに、"new"演算子がいらない。  

2. コンパイラーは、ケースクラスのパラメータリスト内のすべてのパラメータに暗黙のうちに"val"プレフィックスをつける。
	・パラメータはフィールドとして管理される。  
	・イミュータブルな変数として扱える。  

3. コンパイラーがクラスに"toString","hashCode","equals"メソッドの実装を自動で追加してくれる。  
	・”==”でequalsを呼び出すことで、ケースクラス間の比較が容易になる。  

4. コンパイラは変更を加えたコピーを作成するために、"copy"メソッドを追加する。  
	・１つか２つの属性が異なる、ほぼ同じクラスのインスタンスを作成するときに便利。
	・名前付きパラメータを使うことで、変更部分のみことなるインスタンスの作成ができる。

```scala
val v = Var("x")
val op = BinOp("+", Number(1),v)
op.copy(operator = "-")//上のopの"+"が"-"に変わったインスタンスが作成される

```
