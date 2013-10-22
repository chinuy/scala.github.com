---
layout: overview
title: 給Java程式設計師的Scala入門教學
overview: scala-for-java-programmers

disqus: true
multilingual-overview: true
language: zh_TW
---

Michel Schinz與Philipp Haller著
Chikei Lee譯

## 介紹

此教學將對Scala語言以及編譯器做一個簡易的介紹。設定的讀者為具有程設經
驗且想要看Scala功能概要的人。內文假設讀者有著基本、特別是Java上的物件
導向程設知識。

## 第一個例子

這邊用標準的 *Hello world* 程式作為第一個例子。雖然它很無趣，可是這讓
我們在僅用少量語言下演示Scala工具。程式如下：

    object HelloWorld {
      def main(args: Array[String]) {
        println("Hello, world!")
      }
    }

Java程式員應該對這個程式的結構感到熟悉：有著一個 `main` 函式，該函式
接受一個字串陣列引數，也就是命令列引數；函式內容為呼叫已定義好的函式
 `println` 並用Hello world字串當引數。 `main` 函式沒有回傳值(它是程
序函式)。因此並不需要宣告回傳型別。

Java程式員不太熟悉的是包著 `main` 函式的 `object` 宣告。這種宣告引入
我們一般稱之 *Singleton* 的東西，也就是只有一個實體的類別。所以上面的
宣告同時宣告了一個 `HelloWorld` 類別跟一個這類別的實體，也叫做
 `HelloWorld`。該實體會在第一次被使用到的時候即時產生。

眼尖的讀者可能已經注意到這邊 `main` 函式的宣告沒有帶著 `static`。這是
因為Scala沒有靜態成員(函式或資料欄)。Scala程式員將這成員宣告在單實例物件
中，而不是定義靜態成員。

### 編譯這例子

我們用Scala編譯器 `scalac`來編譯這個例子。`scalac` 就像大多數的編譯器
一樣，它接受原碼檔當引數，並接受額外的選項，然後產生一個或多個物件檔。
它產出的物件檔為標準的Java class檔案。

如果我們將上面的程式存成 `HelloWorld.scala` 檔，編譯的指令為( `>` 是提
示字元，不用打)：

    > scalac HelloWorld.scala

這會在現在的目錄產生一些class檔案。其中一個會叫做 `HelloWorld.class`，
裡面包含著可被 `scala` 直接執行的類別。

### 執行範例

一旦編譯過後，Scala程式可以用 `scala` 指令執行。它的使用方式非常的像執
行Java程式的 `java` 指令，並且接受同樣的選項。上面的範例可以用以下的指
令來執行並得到我們預期的輸出：

    > scala -classpath . HelloWorld

    Hello, world!

## 與Java互動

Scala的優點之一是它非常的容易跟Java程式碼溝通。預設匯入所有 `java.lang`  
 底下之類別，其他類別則需要明確匯入。

讓我們看個展示這點的範例。取得現在的日期並根據某個特定的國家排版成該國
的格式，如法國。

Java的標準函式庫定義了一些有用的工具類別，如 `Date` 跟 `DateFormat`。
因為Scala可以無縫的跟Java互動，這邊不需要以Scala實作同樣的類別--我們
只需要匯入對應的Java套件：

    import java.util.{Date, Locale}
    import java.text.DateFormat
    import java.text.DateFormat._

    object FrenchDate {
      def main(args: Array[String]) {
        val now = new Date
        val df = getDateInstance(LONG, Locale.FRANCE)
        println(df format now)
      }
    }

Scala的匯入陳述式跟Java的非常像，但更為強大。如第一行，同一個package
下的多個類別可以用大括號括起來一起導入。另外一個差別是，當要匯入套件或類
別下所有的名稱時，用下標(`_`)而不是星號(`*`)。這是因為星號是一個合法的
Scala識別符號(如函式名稱)。

所以第三行的陳述式導入所有 `DateFormat` 類別的成員。這讓靜態函式
 `getDateInstance` 跟靜態資料欄 `LONG` 可直接被使用。

在 `main` 函式中我們先創造一個Java的 `Date` 類別實體，該實體預設擁有現
在的日期。接下來用 `getDateInstance` 函式定義日期格式。最後根據地區化
的 `DateFormat` 實體對現在日期排版格式化並印出。最後一行展現了一個
Scala有趣的特點。只需要一個引數的函式可以用中綴語法呼叫。就是說，這個表示式

    df format now

是比較不詳細版本的這個表示式

    df.format(now)

這點也許看起來只是一個小小的語法細節，但是他有著重要的後果，其中一個將會在
下一節做介紹。

讓我們以，Scala可以直接繼承Java類別跟實作Java interface，來為這節做結尾。

## 萬物皆物件

Scala是一個純粹的物件導向語言，這句話的意思是說，*所有東西*都是物件，包括
數字、函式。因為Java將基本型別跟參照型別分開，而且沒有辦法像操作變數一樣操作
函式，從這角度來看Scala跟Java是不同的。

### 數字是物件

因為數字是物件，他們也有函式。事實上，一個像底下的算數表示式：

    1 + 2 * 3 / x

只有使用函式呼叫，因為像前一節一樣，該式等價於

    (1).+(((2).*(3))./(x))

這也表示著 `+`、`*` 之類的在Scala裡是合法的識別符號。

因為Scala的詞法分析器對於符號採用最長匹配，在第二版的表示式當中，那些括號
是必要的。也就是說分析器會把這個表示式：

    1.+(2)

拆成 `1.`、`+`、`2` 這三個符號。會這樣拆分是因為 `1.` 既是合法匹配同時又
比 `1` 長。 `1.` 會被解釋成文字 `1.0`，使得他被視為 `Double` 而不是 
 `Int`。把表示式寫成：

    (1).+(2)

可以避免 `1` 被解釋成 `Double`。

### 函式是物件

可能令Java程式員更為驚訝的會是，Scala中函式也是物件。因此，將函式當做引數
傳遞、把它們存入變數、從其他函式返回函式都是可能的。能夠像操作變數一樣的操
作函式這點是*函數編程*這一非常有趣的程設典範的基石之一。

為何把函式當做變數一樣的操作會很有用呢，讓我們考慮一個定時函式，它的功能是
每秒執行一些動作。我們要怎麼將這動作傳給它？最直接的便是將這動作視為函式
傳入。應該有不少的程式員對這種簡單傳遞函式的行為很熟悉：通常在使用者介面相
關的程式上，用以註冊一些當事件發生時被呼叫的回呼函式。

在接下來的程式中，定時函式叫做 `oncePerSecond` ，它接受一個回呼函式做參
數。該函式的型別被寫作 `() => Unit` ，這個型別便是所有無引數且無返回值的
函式的型別( `Unit` 這個型別就像是C/C++的 `void` )。此程式的主函式只是呼
叫定時函式並帶入回呼函式，回呼函式輸出一句話到終端上。也就是說這個程式會不
斷的每秒輸出一次"time flies like an arrow"。

    object Timer {
      def oncePerSecond(callback: () => Unit) {
        while (true) { callback(); Thread sleep 1000 }
      }
      def timeFlies() {
        println("time flies like an arrow...")
      }
      def main(args: Array[String]) {
        oncePerSecond(timeFlies)
      }
    }

值得注意的是，這邊輸出的時候我們使用Scala的函式 `println`，而不是
 `System.out` 裡的函式。

#### 匿名函式

這程式還有改進的空間。第一點，函式 `timeFlies` 只是為了能夠被傳遞進
 `oncePerSecond` 而定義的。賦予一個只被使用一次的函式名字似乎是沒有必要
的，最好能夠在傳入 `oncePerSecond` 時構造出這個函式。Scala可以藉由
*匿名函式*來達到這點。利用匿名函式的改進版本程式如下：

    object TimerAnonymous {
      def oncePerSecond(callback: () => Unit) {
        while (true) { callback(); Thread sleep 1000 }
      }
      def main(args: Array[String]) {
        oncePerSecond(() =>
          println("time flies like an arrow..."))
      }
    }

這例子中的右箭頭 `=>` 告訴我們有一個匿名函式，右箭頭將函式引數跟函式內容
分開。這個例子中，在箭頭左邊那組空的括號告訴我們引數列是空的。函式內容則是
跟先前的 `timeFlies` 裡一樣。

## 類別

之前已講過，Scala是一個物件導向語言，因此它有著類別的概念。(更精確的說，的
確有一些物件導向語言沒有類別的概念，但是Scala不是這類)。Scala宣告類別的
語法跟Java很接近。一個重要的差別是，Scala的類別可以有參數。這邊用底下複數
的定義來展示：

    class Complex(real: Double, imaginary: Double) {
      def re() = real
      def im() = imaginary
    }

這個複數類別接受兩個參數，分別為實跟虛部。在創造 `Complex` 的實體時，必須
傳入這些參數： `new Complex(1.5, 2.3)`。這個類別有兩個函式分別叫做 `re` 
跟 `im` 讓我們取得這兩個部分。

值得注意的是，這兩個函式的回傳值並沒有被明確的給定。編譯器將會自動的推斷，
它會查看這些函式的右側並推導出這兩個函式都會回傳型別為 `Double` 的值。

編譯器並不一定每次都能夠推斷出型別，而且很不幸的是我們並沒有簡單的規則分
辨哪種情況能推斷，哪種情況不能。因為當編譯器無法推斷未明確給定的型別時它會
回報錯誤，實務上這通常不是問題。Scala的初學者在遇到那些看起來很簡單就能推
導出型別的情況時，應該嘗試著忽略型別宣告並看看編譯器是不是也覺得可以推斷。
多嘗試幾次之後程式員應該能夠體會到何時忽略型別、何時該明確指定。

### 無引數函式

函式 `re`、`im` 有個小問題，為了呼叫函式，我們必須在函式名稱後面加上一對空
括號，如這個例子：

    object ComplexNumbers {
      def main(args: Array[String]) {
        val c = new Complex(1.2, 3.4)
        println("imaginary part: " + c.im())
      }
    }

最好能夠在不需要加括號的情況下取得實虛部，這樣便像是在取資料欄。Scala完全可
以做到這件事，需要的只是在定義函式的時候*不要定義引數*。這種函式跟零引數函
式是不一樣的，不論是定義或是呼叫，它們都沒有括號跟在名字後面。我們的
 `Complex` 可以改寫成：

    class Complex(real: Double, imaginary: Double) {
      def re = real
      def im = imaginary
    }

### 繼承與覆寫

Scala中所有的類別都繼承自一個母類別。像前一節的 `Complex` 這種沒有指定的
例子，Scala會暗中使用 `scala.AnyRef`。

Scala中可以覆寫繼承自母類別的函式。但是為了避免意外的覆寫，必須加上 
 `override` 修飾字來明確表示要覆寫函式。我們以覆寫 `Complex` 類別中來自 
 `Object` 的  `toString` 作為範例。

    class Complex(real: Double, imaginary: Double) {
      def re = real
      def im = imaginary
      override def toString() =
        "" + re + (if (im < 0) "" else "+") + im + "i"
    }


## Case Class跟模式匹配(pattern matching)

樹是常見的資料結構。如：解譯器跟編譯器內部常見的表示程式方式便是樹；XML文件
是樹；還有一些容器是根基於樹，如紅黑樹。

接下來我們會藉由一個小型計算機程式來看看Scala是如何呈現並操作樹。這個程式
的功能將會是足以操作簡單、僅含有整數、常數、變數跟加法的算術式。`1+2` 跟
 `(x+x)+(7+y)`為兩個例子。

我們得先決定這種表示式的表示法。最自然表示法便是樹，其中節點是操作、葉點是
值。

Java中我們會將這個樹用一個抽象母類別表示，然後每種節點跟葉點分別有各自的
實際類別。在函數邊程裡會用代數資料類型。Scala則是提供了介於兩者之間的
*case class*。將它運用在這邊會是如下：

    abstract class Tree
    case class Sum(l: Tree, r: Tree) extends Tree
    case class Var(n: String) extends Tree
    case class Const(v: Int) extends Tree

`Sum`、`Var`、`Const` 類別定義成case class代表著它們跟一般類別有所差別：

- 在創建類別實體時不需要用 `new`(也就是說我們可以寫 `Const(5)` ，而不是
  `new Const(5)`)。
- 對應所有的建構式參數，Scala會自動定義對應的取值函式(即，對於 `Const` 
  類別的實體，我們可以直接用 `c.v` 來取得建構式中的 `v` 參數)。
- `equals` 跟 `hashCode` 會有預設的定義。該定義會根據實體的*結構*而不是
  個別實體的識別來運作。
- `toString` 會有預設的定義。會印出"原始型態"(即，`x+1` 的樹會被印成
   `Sum(Var(x),Const(1))`)。
- 這些類別的實體可以藉由*模式匹配*來拆解。

現在我們有了算術表示式的資料型別，可以開始定義各種運算。我們將從一個可以
在*環境*內對運算式求值的函式起頭。環境的用處是賦值給變數。舉例來說，運算
式 `x+1` 在一個將 `x` 賦與 `5` 的環境(寫作 `{ x -> 5 }` )下求值會得
到 `6`。

因此我們需要一個表示環境的方法。當然我們可以用一些像是雜湊表的關連性資料
結構，但是我們也可以直接用函式！環境就只是一個將值對應到(變數)名稱的函式
。之前提到的環境 `{ x -> 5 }` 在Scala中可以簡單的寫作：

    { case "x" => 5 }

這串符號定義了一個當輸入是字串 `"x"` 的時候回傳整數 `5`，其他輸入則是用
例外表示失敗的函式。

開始實作之前，讓我們先給環境型別一個名字。當然，我們可以直接用
 `String => Int`，但是給這型別名字可以讓我們簡化程式，而且在未來要改動
的時候較為簡便。在Scala我們是這樣表示這件事：

    type Environment = String => Int

於是型別 `Environment` 便可以當做輸入 `String` 回傳 `Int` 函式的型別的
代名。

現在我們可以給出求值函式的實作。概念上非常的簡單：兩個表示式和的值是兩個
表示式值的和；變數的值直接從環境取值；常數的值就是常數本身。表示這些在
Scala裡並不困難：

    def eval(t: Tree, env: Environment): Int = t match {
      case Sum(l, r) => eval(l, env) + eval(r, env)
      case Var(n)    => env(n)
      case Const(v)  => v
    }

這個求值函式藉由對樹 `t` 做*模式匹配*來求值。上述實作的意思應該從直觀上
便很明確：

1. 首先檢查樹 `t` 是否為 `Sum`，如果是的話將左/右側子樹綁定到新變
   數 `l`/`r`，然後再對箭頭後方的表示式求值；這一個表示式可以使用(而且這
   邊也用到)根據箭頭左側模式所綁定的變數，也就是 `l` 跟 `r`，
2. 如果第一個檢查失敗，也就是說樹不是 `Sum`，接下來檢查 `t` 是否為 `Var`
   ，如果是的話將 `Var` 所帶的名稱綁定到變數 `n` 並求值右側的表示式，
3. 如果第二個檢查也失敗，表示樹不是 `Sum` 也不是 `Var`，那便檢查是不是
    `Const`，如果是的話將 `Const` 所帶的名稱綁定到變數 `v`  並求值右
   側的表示式，
4. 最後，如果全部的檢查都失敗，會丟出例外表示匹配失敗；這只會在有更多
    `Tree` 的子類別的情況下發生。

如上，模式匹配基本上就是嘗試將一個值對一系列的模式做匹配，並在一個模式成
功的匹配時抽取並命名該值的各部分，最後對一些程式碼求值，而這些程式碼通常
會利用被命名到的部位。

一個經驗豐富的物件導向程式員也許會疑惑為何我們不將 `eval` 定義成 `Tree`
類別跟子類的*函式*。由於Scala允許在case class中跟一般的類別一樣定義函式，
事實上我們可以這樣做。要用模式匹配或是函式只是品味的問題，但是這會對擴充
性有重要的影響。

- 當使用函式的時候，只要定義新的 `Tree` 子類便新增新的節點，相當的容易。
  另一方面，增加新的操作需要修改所有的子類，很麻煩。
- 當使用模式匹配的時候情況則反過來：增加新節點需要修改所有對樹做模式匹配
  的函式將新節點納入考慮；增加新的操作則很簡單，定義新的函式就好。

讓我們定義新的操作以更進一步的探討模式匹配：對符號求導數。讀者們可能還記
得這個操作的規則：

1. 和的導數是導數的和
2. 如果是對變數 `v` 取導數，變數 `v` 的導數是1，不然就是0
3. 常數的導數是0

這些規則幾乎可以從字面上直接翻成Scala程式碼：

    def derive(t: Tree, v: String): Tree = t match {
      case Sum(l, r) => Sum(derive(l, v), derive(r, v))
      case Var(n) if (v == n) => Const(1)
      case _ => Const(0)
    }

這個函式引入兩個關於模式匹配的新觀念。首先，變數的 `case` 運算式有一個
*看守*，也就是 `if` 關鍵字之後的表示式。除非表示式求值為真，不然這個看守
會讓匹配直接失敗。在這邊是用來確定我們只在取導數變數跟被取導數變數名稱相
同時才回傳常數 `1`。第二個新特徵是可以匹配任何值的*萬用字元* `_`。

我們還沒有探討完模式匹配的全部功能，不過為了讓這份文件保持簡短，先就此打
住。我們還是希望能看到這兩個函式在真正的範例如何作用。因此讓我們寫一個
簡單的 `main` 函數，對表示式 `(x+x)+(7+y)` 做一些操作：先在環境
 `{ x -> 5, y -> 7 }` 下計算結果，然後在對 `x` 接著對 `y` 取導數。

    def main(args: Array[String]) {
      val exp: Tree = Sum(Sum(Var("x"),Var("x")),Sum(Const(7),Var("y")))
      val env: Environment = { case "x" => 5 case "y" => 7 }
      println("Expression: " + exp)
      println("Evaluation with x=5, y=7: " + eval(exp, env))
      println("Derivative relative to x:\n " + derive(exp, "x"))
      println("Derivative relative to y:\n " + derive(exp, "y"))
    }

執行這程式，得到預期的輸出：

    Expression: Sum(Sum(Var(x),Var(x)),Sum(Const(7),Var(y)))
    Evaluation with x=5, y=7: 24
    Derivative relative to x:
     Sum(Sum(Const(1),Const(1)),Sum(Const(0),Const(0)))
    Derivative relative to y:
     Sum(Sum(Const(0),Const(0)),Sum(Const(0),Const(1)))

研究這輸出我們可以發現，取導數的結果應該在輸出前更進一步的化簡。用模式
匹配實作一個基本的化簡函數是一個很有趣(但是意外的棘手)的問題，在這邊
留給讀者當練習。

## 特質(Traits)

除了由母類別繼承行為以外，Scala類別還可以從一或多個*特質*導入。

對一個Java程式員最簡單去理解特質的方式應該是視他們為帶有實作的介面。在
Scala裡，當一個類別繼承特質時，他實作了該特質的介面並繼承所有特質帶有的
功能。

為了理解特質的用處，讓我們看一個經典範例：有序物件。大部分的情況下，一個
類別所產生出來的物件之間可以互相比較大小是很有用的，如排序他們。在Java
裡可比較大小的物件實作 `Comparable` 介面。在Scala中藉由定義等價於 
 `Comparable` 的特質 `Ord`，我們可以做的比Java稍微好一點。

當在比較物件的大小時，有六個有用且不同的謂詞(predicate)：小於、小於等於
、等於、不等於、大於等於、大於。但是把六個全部都實作很煩，尤其是當其中有四
個可以用剩下兩個表示的時候。也就是說，(舉例來說)只要有等於跟小於謂詞，我
們就可以表示其他四個。在Scala中這些觀察可以很漂亮的用下面的特質宣告呈現：

    trait Ord {
      def < (that: Any): Boolean
      def <=(that: Any): Boolean =  (this < that) || (this == that)
      def > (that: Any): Boolean = !(this <= that)
      def >=(that: Any): Boolean = !(this < that)
    }

這份定義同時創造了一個叫做 `Ord` 的新型別，跟Java的 `Comparable` 介
面有著同樣的定位，且給了一份以第一個抽象謂詞表示剩下三個謂詞的預設實作。
因為所有的物件預設都有一份等於跟不等於的謂詞，這邊便沒有定義。

上面使用了一個 `Any` 型別，在Scalla中這個型別是所有其他型別的母型別。
因為它同時也是基本型別如 `Int`、`Float`的母型別，可以將其視為更為一般化
的Java `Object` 型別。

因此只要定義測試相等性跟劣性的謂詞，並且加入 `Ord`，就可以讓一個類別的
物件們互相比較大小。讓我們實作一個表示陽曆日期的 `Date` 類別來做為例子。
這種日期是由日、月、年組成，我們將用整數來表示這三個資料。因此我們可以定
義 `Date` 類別為：

    class Date(y: Int, m: Int, d: Int) extends Ord {
      def year = y
      def month = m
      def day = d
      override def toString(): String = year + "-" + month + "-" + day

這邊要注意的是宣告在類別名稱跟參數之後的 `extends Ord`。這個語法宣告了
 `Date` 繼承 `Ord` 特質。

然後我們重新定義來自 `Object` 的 `equals` 函式好讓這個類別可以正確的
根據每個資料欄來比較日期。因為在Java中 `equals` 預設實作是直接比較實際
物件本身，並不能在這邊用。於是我們有下面的實作：

    override def equals(that: Any): Boolean =
      that.isInstanceOf[Date] && {
        val o = that.asInstanceOf[Date]
        o.day == day && o.month == month && o.year == year
      }

這個函式使用了預定義函式 `isInstanceOf` 跟 `asInstanceOf`。
 `isInstanceOf` 對應到Java的 `instanceof` 運算子，只在當使用它的物
件的型別跟給定型別一樣時傳回真。 `asInstanceOf` 對應到Java的轉型運算
子，如果物件是給定型別的實體，該物件就會被視為給定型別，不然就會丟出
 `ClassCastException` 。

最後我們需要定義測試劣性的謂詞如下。

    def <(that: Any): Boolean = {
      if (!that.isInstanceOf[Date])
        error("cannot compare " + that + " and a Date")

      val o = that.asInstanceOf[Date]
      (year < o.year) ||
      (year == o.year && (month < o.month ||
                         (month == o.month && day < o.day)))
    }

這邊使用了另外一個預定義函式 `error`，它會丟出帶著給定錯誤訊息的例外。
這便完成了 `Date` 類別。這個類別的實體可被視為日期或是可比較物件。而且
他們通通都定義了之前所提到的六個比較謂詞： `equals`跟`<` 直接出現在類
別定義當中，其他的則是繼承自 `Ord` 特質。

特質在其他場合也有用，不過詳細的探討它們的用途並不在本文件目標內。

## 泛型

在這份教學裡，我們最後要探討的Scala特性是泛型。Java程式員應該相當的清楚
在Java 1.5之前缺乏泛型所導致的問題。

泛型指的是能夠將型別也作為程式參數的功能。舉例來說，當程式員在為鏈結串列
寫函式庫的時候，他必須決定串列的元素型別為何。由於這串列是要在許多不同的
場合使用，不可能決定串列的元素型別為如 `Int` 一類。這樣限制太多。

Java程式員採用所有物件的母類別 `Object`。這個解決辦法並不理想，一方面
這並不能用在基礎型別(`int`、`long`、`float`之類)，再來這表示必須靠程式
員手動加入大量的動態轉型。

Scala藉由可定義泛型類別(跟函式)來解決這問題。讓我們藉由最簡單的類別容器
來檢視這點：參照，它可以是空的或者指向某型別的物件。

    class Reference[T] {
      private var contents: T = _
      def set(value: T) { contents = value }
      def get: T = contents
    }

類別 `Reference` 帶有一個型別參數 `T`，這個參數會是容器內元素的型別。
此型別被用做 `contents` 變數的型別、 `set` 函式的引數型別、 `get` 函
式的回傳型別。

上面的程式碼使用的Scala的變數語法，應該不需要過多的解釋。值得注意的是賦
與該變數的初始值是 `_`，該語法表示預設值。數值型別的預設值是0，
 `Boolea8n` 型別是偽， `Unit` 型別是 `()` ，所有的物件型別是 `null`。

為了使用 `Reference` 類型，我們必須指定 `T`，也就是這容器所包容的元素
型別。舉例來說，創造並使用該容器來容納整數，我們可以這樣寫：

    object IntegerReference {
      def main(args: Array[String]) {
        val cell = new Reference[Int]
        cell.set(13)
        println("Reference contains the half of " + (cell.get * 2))
      }
    }

如例子中所展現，並不需要先將 `get` 函式所回傳的值轉型便能當做整數使用。
同時因為被宣告為儲存整數，也不可能存除了整數以外的東西到這一個容器中。

## 結語

本文件對Scala語言做了快速的概覽並呈現一些基本的例子。對Scala有更多興趣
的讀者可以閱讀有更多進階範例的 *Scala By Example*，並在需要的時候參閱
 *Scala Language Specification* 。
