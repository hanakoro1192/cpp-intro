# メモリーを無限に確保する

## これまでのまとめ

ここまで読み進めてきた読者は、逐次実行、条件分岐、ループに加えて、変数と関数を理解した。これだけの要素を習得したならば、本質的にはプログラミングはほぼできるようになったと言ってよい。ただし、まだできないことがある。動的なメモリー確保だ。

標準入力から`0`が入力されるまで任意個の整数値を受け取り、小さい値から順に出力するプログラムを実装しよう。以下はそのようなプログラムの実行例だ。

~~~
$ make run
100
-100
1
6
3
999
-5000
0
-5000
-100
1
3
6
100
999
~~~

`0`が入力されるまで、1番目に、2番目に小さい値はわからない。そのため、この問題の解決には、入力をすべて保持しておく必要がある。

ここで必要なのは、値をいくらでも保持しておく方法と、値に順番があり、$i$番目の値を間接的に指定して読み書きできる方法だ。その方法としてC++には標準ライブラリ`std::vector`がある。

## vector

`std::vector<T>`は`T`型の値をいくらでも保持できる。`T`には保持する値の型を指定する。例えば`int`とか`double`とか`std::string`だ。

~~~cpp
int main()
{
    // 整数型intの値を保持するvector
    std::vector<int> vi ;

    // 浮動小数点数型doubleの値を保持するvector
    std::vector<double> vd ;

    // 文字列型std::stringの値を保持するvector
    std::vector<std::string> vs ;
}
~~~

`std::vector<T>`というのはそれ自体が型になっている。そして`T`には型を指定する。ということは、`vector`型の値を保持する`vector`も書けるということだ。

~~~cpp
int main()
{
    // 整数型intを保持するvectorを保持するvector
    std::vector< std::vector< int > > vvi ;
}
~~~

もちろん、上の`vector`を保持する`vector`も書ける。その場合、`std::vector<std::vector<std::vector<int>>>`になる。この`vector`を保持する`vector`も当然書けるが省略する。

`std::vector`型の変数にはメンバー関数`push_back`を使うことで値を保持できる。

~~~cpp
int main()
{
    std::vector<int> v  ;

    v.push_back(1) ;
    v.push_back(2) ;
    v.push_back(3) ;
}
~~~

`メンバー関数`(member function)というのは特別な関数で、詳細はまだ説明しない。ここで覚えておくべきこととしては、メンバー関数は一部の変数に使うことができること、メンバー関数`f`を変数`x`に使うには`'x.f(...)'`のように書くこと、を覚えておこう。

`std::vector`はメモリーの続く限りいくらでも値を保持できる。試しに1000個の整数を保持させてみよう。

~~~cpp
int main()
{
    std::vector<int> v ;

    for ( int i = 0 ; i != 1000 ; ++i )
    {
        v.push_back( i ) ;
    }
}
~~~

このプログラムは`0`から`999`までの1000個の整数を`std::vector`に保持させている。

`std::vector`では保持する値のことを要素という。要素は順番を持っている。メンバー関数`push_back`は最後の要素の次に要素を追加する。最初に要素はない。もしくは0個ある空の状態だと言ってもよい。

~~~cpp
int main()
{
    std::vector<int> v ;

    // vは空

    // 要素数1、中身は{1}
    v.push_back(1) ;
    // 要素数2、中身は{1,2}
    v.push_back(2) ;
    // 要素数3、中身は{1,2,3}
    v.push_back(3) ;
}
~~~

`std::vector`はメンバー関数`size()`で現在の要素数を取得できる。

~~~cpp
int main()
{
    std::vector<int> v ;

    // 0
    std::cout << v.size() ;
    v.push_back(1) ;
    // 1
    std::cout << v.size() ;
    v.push_back(2) ;
    // 2
    std::cout << v.size() ;
}
~~~


せっかく値を入れたのだから取り出したいものだ。`std::vector`ではメンバー関数`at(i)`を使うことで、`i`番目の要素を取り出すことができる。この`i`のことを添字、インデックスと呼ぶ。ここで注意してほしいのは、最初の要素は0番目で、次の要素は1番目だということだ。最後の要素は`size()-1`番目になる。

~~~cpp
int main()
{

    std::vector<int> v ;

    for ( int i = 0 ; i != 10 ; ++i )
    {
        v.push_back(i) ;
    }

    // vの中身は{0,1,2,3,4,5,6,7,8,9}

    // 0, 0番目の最初の要素
    std::cout << v.at(0) ;
    // 4, 4番目の要素
    std::cout << v.at(4) ;
    // 9, 9番目の最後の要素
    std::cout << v.at(9) ;
}
~~~

この例ではループを使っている。読者はすでにループについては理解しているはずだ。上のコードが理解できないのであれば、もう一度ループの章に戻って学び直すべきだ。


もし`at(i)`に要素数を超える`i`を渡してしまった場合どうなるのだろうか。

~~~cpp
int main()
{
    std::vector<int> v { } ;
    v.push_back(0) ;
    // vには0番目の要素しかない
    // 1番目は誤り
    std::cout << v.at(1) ;
}
~~~

実行して確かめてみよう。

~~~
$ ./program
terminate called after throwing an instance of 'std::out_of_range'
  what():  vector::_M_range_check: __n (which is 1) >= this->size() (which is 1)
Aborted (core dumped)
~~~

なにやら恐ろしげなメッセージが表示されるではないか。しかし心配することはない。このメッセージはむしろうれしいメッセージだ。変数`v`に1番目の要素がないことを発見してくれたという実行時のエラーメッセージだ。すでに学んだように、エラーメッセージは恐れるものではない。エラーメッセージはうれしいものだ。エラーメッセージが出たらありがとう。エラーメッセージがあるおかげでバグの存在がわかる。

このメッセージの本当の意味はいずれ例外やデバッガーを解説する章で説明するとして、`vector`の要素数を超える指定をしてはいけないことを肝に銘じておこう。もちろん、`-1`もダメだ。

メンバー関数`at(i)`に与える引数`i`の型は整数型ではあるのだが`int`型ではない。`std::size_t`型という特殊な型になる。メンバー関数`size`も同様に`std::size_t`型を返す。

~~~cpp
int main()
{
    std::vector<int> v ;

    // std::size_t型
    std::size_t size = v.size() ;

    v.push_back(0) ;

    // std::size_t型
    std::size_t index = 0 ;
    v.at( index ) ;
}
~~~

なぜ`int`型ではダメなのか。その謎は整数の章で明らかになる。ここでは`std::size_t`型は負数が使えない整数型だということだけ覚えておこう。`std::size_t`型に`-1`はない。`vector`の要素指定では負数は使えないので、負数が使えない変数を使うのは理にかなっている。

さて、これまでに学んだ知識だけを使って、`std::vector`のすべての要素を順番どおりに出力するコードが書けるはずだ。

~~~cpp
int main()
{

    std::vector<int> v ;

    for ( int iota = 0 ; iota != 10 ; ++iota )
    {
        v.push_back(iota) ;
    }

    for ( std::size_t index = 0 ; index != v.size() ; ++index )
    {
        std::cout << v.at(index) << " "s ;
    }
}
~~~

このコードが書けるということは、もう標準入力から`0`が入力されるまで任意個の値を受け取り、入力された順番で出力するプログラムも書けるということだ。

~~~cpp
int input()
{
    int x{} ;
    std::cin >> x ;
    return x ;
}
int main()
{
    std::vector<int> v ;
    int x { } ;

    // 入力
    while ( ( x = input() ) != 0 )
    {
        v.push_back( x ) ;
    }

    // 出力
    for ( std::size_t index = 0 ; index != v.size() ; ++index )
    {
        std::cout << v.at(index) << " "s ;
    }
}
~~~

入力された順番に出力できるということは、その逆順にも出力できるということだ。

~~~cpp
for ( std::size_t index = v.size()-1 ; index != 0 ; --index )
{
    std::cout << v.at(index) << " "s ;
}

std::cout << v.at(0) ;
~~~

最後に`'v.at(0)'`を出力しているのは、ループが`'i == 0'`のときに終了してしまうからだ。つまり最後に出力すべき`vector`最初の要素である`'v.at(0)'`が出力されない。

`std::size_t`型は`-1`が使えないため、このようなコードになってしまう。`int`型を使えば負数は使えるのだが、`int`型と`std::size_t`型の比較はさまざまな理由で問題がある。その理由は整数の章で深く学ぶことになるだろう。


ところで、問題は入力された整数を小さい順に出力することだった。この問題を考えるために、まず`vector`の中に入っている要素から最も小さい整数の場所を探すプログラムを考えよう。

問題を考えるにあたって、いちいち標準入力から入力を取るのも面倒なので、あらかじめ`vector`に要素を入れておく方法を学ぶ。実は、`vector`の要素は以下のように書けば指定することができる。

~~~cpp
int main()
{
    // 要素{1, 2, 3}
    std::vector<int> v = { 1,2,3 } ;

    // 1
    auto x = v.at(0) ;
    // 2
    auto y = v.at(1) ;
    // 3
    auto z = v.at(2) ;
}
~~~

この例では、`1`, `2`, `3`の整数が書かれた順番であらかじめ`vector`の要素として入った状態になる。

さて、以下のような要素の`vector`から最も小さい整数を探すプログラムを考えよう。

~~~cpp
std::vector<int> v = { 8, 3, 7, 4, 2, 9, 3 } ;
~~~

これを見ると、最も小さい整数は4番目(最初の要素は0番目なので4番目)にある`2`だ。ではどうやって探すのだろうか。

解決方法としては先頭から末尾まで要素を1つずつ比較して、最も小さい要素を見つけ出す。まず0番目の`8`が最も小さいと仮定する。現在わかっている中で最も小さい要素のインデックスを記録するために変数`min`を作っておこう。

~~~
min = 0
8 3 7 4 2 9 3
^
~~~

次に1番目の`3`と`min`番目を比較する。1番目の方が小さいので変数`min`に`1`を代入する。

~~~
min = 1
8 3 7 4 2 9 3
  ^
~~~


2番目の`7`と`min`番目を比較するとまだ1番目の方が小さい。3番目の4と比較してもまだ`min`番目の方が小さい。

4番目の`2`と`min`番目を比較すると、4番目の方が小さい。変数`min`に`4`を代入しよう。

~~~
min = 4
8 3 7 4 2 9 3
        ^
~~~

5番目と6番目も`min`番目より大きいので、これで変数`min`に代入された4番目の要素が最も小さいことがわかる。

`vector`の変数を`v`、要素数を`size`とする。変数`min`には現在わかっている中で最も小さい要素へのインデックスが代入される。

1. 変数`min`に0を代入する
2. `size`回のループを実行する
3. 変数`index`に`0`から`size-1`までの整数を代入する
4. `'v.at(index) < v.at(min)'`ならば`min = index`

さっそく書いてみよう。

~~~cpp
int main()
{
    // vectorの変数をv
    std::vector<int> v = { 8, 3, 7, 4, 2, 9, 3 } ;
    // 要素数をsizeとする
    std::size_t size = v.size() ;

    // 変数minに0を代入する
    std::size_t min = 0 ;

    // size回のループを実行する
    // 変数iに0からsize-1までの整数を代入する
    for ( std::size_t index = 1 ; index != size ; ++index )
    {
        // 'v.at(index) < v.at(min)'ならばmin = index
        if ( v.at(index) < v.at(min) )
            min = index ;
    }

    // 一番小さい値を出力
    std::cout << v.at(min) ;
}
~~~

うまくいった。

ところで、最終的に解きたい問題とは、`vector`のすべての要素を小さい順に出力するということだ。すると、もっと小さい要素を出力した次に、2番目に小さい要素、3番目に小さい要素・・・と出力していく必要がある。

2番目に小さい要素を見つけるためには、1番目に小さい要素を探さなければよい。そこで、発見した最も小さい要素と先頭の要素を交換してしまい、先頭は無視して最も小さい要素を探すことを繰り返すと実現できる。

例えば以下のような要素があるとして、

~~~
8 3 7 4 2 9 3
        ^
~~~

最も小さい要素である4番目の`2`と0番目の`8`を交換する。

~~~
2 3 7 4 8 9 3
^       ^
+-------+
~~~

そして、0番目は無視して最も小さい要素を探す。

~~~
3 7 4 8 9 3
^
~~~

この場合、最も小さいのは0番目と5番目の`3`だ。どちらも同じだが今回は0番目を選ぶ。もともと0番目にあるので0番目と0番目を交換した結果は変わらない。

そして、新しい0番目は無視して最も小さい要素を探す。

~~~
7 4 8 9 3
        ^
~~~

今度は4番目の`3`だ。これも先頭と交換する

~~~
3 4 8 9 7
^       ^
+-------+
~~~

これを繰り返していけば、小さい順に要素を探していくことができる。

この処理を行うコードを考えるために、先ほどと似たようなコードを見てみよう。

~~~cpp
int main()
{
    std::vector<int> v = { 8, 3, 7, 4, 2, 9, 3 } ;
    std::size_t size = v.size() ;

    // この部分を繰り返す？ 
    { // これ全体が1つのブロック文
        std::size_t min = 0 ;

        for ( std::size_t index = 1 ; index != size ; ++index )
        {
            if ( v.at(index) < v.at(min) )
                min = index ;
        }

        // 出力
        std::cout << v.at(min) << " "s ;

        // 先頭と交換
    }
}
~~~

このコードはそのまま使えない。今回考えた方法では、先頭が1つずつずれていく。そのために、最も小さい要素を探すループを、さらにループさせる。

~~~cpp
// 現在の先頭
for ( std::size_t head = 0 ; head != size ; ++head )
{
    // 現在の先頭であるmin番目を仮の最小の要素とみなすのでhead
    std::size_t min = head ;    
    // 現在の先頭の次の要素から探すのでhead + 1
    for ( std::size_t index = head + 1 ; index != size ; ++index )
    {
        if ( v.at(index) < v.at(min) )
            min = index ;
    }

    std::cout << v.at(min) << " "s ;

    // 先頭と交換
}
~~~

次に先頭(0番目)と現在見つけた最小の要素(`min`番目)を交換する方法を考えよう。

`vector`の`n`番目の要素の値を`x`に変更するには、単に`v.at(n) = x`と書けばよい。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3} ;

    v.at(0) = 4 ;
    // vは{4,2,3}
}
~~~

すると、`vector`の`i`番目の要素に`j`番目の要素値を入れるには`'v.at(i) = v.at(j)'`と書く。

~~~cpp
int main()
{
    std::vector<int> v = {1,2,3} ;
    v.at(0) = v.at(2) ;
    // vは{3,2,3}
}
~~~

変数とまったく同じだ。

しかし、変数`a`に変数`b`の値を代入すると、変数`a`の元の値は消えてしまう。

~~~cpp
int main()
{
    int a = 1 ;
    int b = 2 ;

    // aの元の値は上書きされる
    a = b ;
    // a == 2
    b = a ;
    // b == 2
}
~~~

変数`a`, `b`の値を交換するためには、変数への代入の前に、別の変数に値を一時退避しておく必要がある。

~~~cpp
int main()
{
    int a = 1 ;
    int b = 2 ;

    // 退避
    auto temp = a ;

    a = b ;
    b = temp ;

    // a == 2
    // b == 1
}
~~~

さて、これで問題を解く準備はすべて整った。

~~~cpp
int main()
{
    std::vector<int> v = { 8, 3, 7, 4, 2, 9, 3 } ;
    std::size_t size = v.size() ;
   
    // 先頭をずらすループ 
    for ( std::size_t head = 0 ; head != size ; ++head )
    {
        std::size_t min = head ;
        // 現在の要素の範囲から最小値を見つけるループ
        for ( std::size_t index = head+1 ; index != size ; ++index )
        {
            if ( v.at(index) < v.at(min) )
                min = index ;
        }
        // 出力
        std::cout << v.at(min) << " "s ;

        // 最小値を先頭と交換
        auto temp = v.at(head) ;
        v.at(head) = v.at(min) ;
        v.at(min) = temp ;
    }

    // 実行したあと
}
~~~

ところで、このプログラムの「実行したあと」地点での`vector`の中身はどうなっているだろうか。

~~~cpp
int main()
{
    std::vector<int> v = { 8,3,7,4,2,9,3 } ;

    // 上と同じコードなので省略

    // 実行したあと
    std::cout << "\n"s ;
    
    for ( std::size_t index = 0, size = v.size() ; index != size ; ++index )
    {
        std::cout << v.at(index) << " "s ;
    }
}
~~~

これを実行すると以下のようになる。

~~~
$ make run
2 3 3 4 7 8 9
2 3 3 4 7 8 9
~~~

なんと`vector`の要素も小さい順に並んでいる。この状態のことを、ソートされているという。ループの中で最も小さい値を出力していく代わりに、まずソートして先頭から値を出力してもよいということだ。

ソートにはさまざまな方法があるが、今回使ったのは選択ソート(selection sort)というアルゴリズムだ。

`vector`を使う方法には、イテレーターというもっと便利な方法があるが、それはイテレーターの章で説明する。


