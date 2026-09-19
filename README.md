# BrainfuckOnTeX

`brainfuck.tex` を読み込み、本文で
```tex
\brainfuck{++++++++++[>+++++++>++++++++++>+++>+<<<<-]>++.>+.+++++++..+++.>++.<<+++++++++++++++.>.+++.------.--------.>+.}
```
のように記述すると `Hello World!` と出力されます。構文は以下の通りです。
```tex
\brainfuck[<input>]{<code>}
```
`<input>` に指定した文字列は `,` の実行時に左から1文字ずつ消費されていきます。したがって，
```tex
\brainfuck[Hello, human.]{>+++[<++++>-]<+[>,.<-]}
```
は `Hello, human.` と出力されます。
また `\char`を使用するため、使用環境によってはグリフの出力がうまくいかない可能性があります。

`brainfuck.sty` は、LaTeX 向けにより充実したオプションを使用可能な環境を提供します。プリアンブル部で `\usepackage{brainfuck}` または `\RequirePackage{brainfuck}` として読み込んだ時、内部で `brainfuck.tex` を読み込もうとすることに注意してください。
```tex
\begin{brainfuckcode*}[output=\foo, output format=ascii]
++++++++[>++++[>++>+++>+++>+<<<<-]>+>+>->>+[<]<-]>>.>---.+++++++..+++. Output `Hello'
>>.<-.<.+++.------.--------.>>+. Output ` World!'
\end{brainfuckcode*}
\foo% Expand into `72 101 108 108 111 32 87 111 114 108 100 33 '
```

使用可能なオプション名とその値は以下の通りです。
- `memory`: コード実行後にテープの状態を記憶するかを指定する。デフォルトは `false`。
  - `true`
  - `false`
- `initial`: コード実行前にテープの状態を初期化するかを指定する。デフォルトは `true` だが、`memory` が `true` だと次回以降実行時のデフォルトが `false` になる。
  - `true`
  - `false`
- `output`: コードの出力先を指定する。値は制御綴りであればなんでもよい。指定した制御綴りが未定義か否かで、`new output` か `add output` に渡し直される。
- `new output`: コードの出力先を指定する。値は制御綴りであればなんでもよい。出力を上書きする。
- `add output`: コードの出力先を指定する。値は制御綴りであればなんでもよい。出力を追加する。
- `output format`: 出力形式を指定する。デフォルトは `char`。
  - `char`: `\char` による出力。
  - `ascii`: 8ビット ASCII コードによる出力。
  - `unicode`: 2桁 Unicode による出力。
- `input`: 入力を指定する。

オプション指定時の `=` の前後と `,` の前後の半角スペースおよび改行文字は無視されます。また、`input` を除くオプションでは、値はすべて小文字に変換されてから渡されます。

`\brainfuck` のオプションでは空白文字や `,`、`=` をそのまま渡すことができますが、`brainfuckcode*` 環境では渡せないことに注意してください。そこで `brainfuckcode*` 環境のオプションでは、入力を ASCII コードで指定する仕組みが用意されています。
```tex
\begin{brainfuckcode*}[input = Hello \encoded{44 32} human.]
>+++[<++++>-]<+[>,.<-] Output `Hello, human.'
\end{brainfuckcode*}
```
また、`,` 実行時の入力の消費を抑制する仕組みも用意されています。
```tex
\begin{brainfuckcode*}[input = \encoded{10} AH \fix{!} \encoded{32} Hello \encoded{44 32} Sir!]
,[>,.<-] Output `AH!!!!!!!!'
\end{brainfuckcode*}
```
`\fix` に空白文字や `,`、`=` を渡すことはあまりおすすめしません。そこで `\encoded` とネストすることで、代わりに ASCII コードとして渡すことができます。`\fix` と `\encoded` のネストには互換性があり、`\fix` どうしのネストは `\fix` 一つにまとめられます。`\encoded` どうしのネストはできないため注意してください。`\encoded` に渡される値は数値リテラルか、空白文字で区切られた数値リテラルの列、あるいは `\fix` である必要があります。
`\encoded` に渡すことのできる数値は任意精度です。`,` 実行時に、256 の剰余が計算されて現在のポインタの値にコピーされます。
