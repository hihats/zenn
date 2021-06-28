---
title: "「DIは必ずしも善ではない」| Dependency injection is not a virtue by DHH"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ruby", "DI", "test"]
published: true
---

DHHの [Dependency injection is not a virtue(2013)](https://dhh.dk/2012/dependency-injection-is-not-a-virtue.html) という記事は有名ですが、ちゃんとした日本語訳が意外とないようなので、書き出してみて思ったことを要約してみた。[^1]
Rubyのエンジニアの中には、何も考えずに他のモデルのnewを書いてる人の割合が多いという（コードレビュー時のヒアリングによる）体感があり、また8年前の記事なので経験の浅い人は読んだことがない人もいると思うので是非多くの方に読んでほしい。
全部読む時間が無い人は[要約](#要約)へ

## 原文と訳文


> In languages less open than Ruby, hard-coded class references can make testing tough. If your Java code has Date date = new Date(); buried in its guts, how do you set it to a known value you can then compare against in your tests? Well, you don't. So what you do instead is pass in the date as part of the parameters to your method. You inject the dependency on Date. Yay, testable code!

Rubyほどオープンではない言語では、ハードコードされたクラス参照はテストを困難にします。もしあなたのJavaのメソッドのど真ん中に `Date date = new Date();` が埋め込まれていたら、どうやってそれを既知の値に設定し、テストで比較できますか？ そこで値を設定するのではありません。メソッドのパラメータの一部として日付を渡すようにします。そうやって`Date`への依存性を注入します。
Yay！テスタブルコードになった！

> As has unfortunately happened with a variety of patterns that originate from rigid languages like Java, Dependency Injection has spread and been advocated as a cross-language best practice on trumped up benefits of flexibility and malleability. If your code never knows exactly who it's talking to, it can talk to anyone! Testing stubs, mocks, and future collaborators. Hogwash.

> Ruby is like Play-Doh not LEGO and the ways it'll bend to extract the best of hard coding is truly impressive. Take the publish! example from Adam Keys' Design for test vs design for API:

Javaのような厳格な言語から生まれた様々なパターンで（不幸にも）起こったように、依存性注入は「柔軟性と可鍛性という切り札を持ったクロスランゲージのベストプラクティス」として広まり、提唱されました。コードが誰と話しているのかを正確に知らないということは、誰とでも話すことができるということです。スタブ、モック、そして未来の共同作業者。バカげている！[^2]

Rubyはレゴではなく粘土のおもちゃのようなもので、ハードコーディングの長所を引き出すための変形させ方は実に**印象的**です。Adam Keysの「Design for test vs design for API」にある `publish!` の例を見てみましょう。


```
def publish!
  self.update published_at: Time.now
end
```

> In less open languages that's an obvious problem to test. But in Ruby it couldn't be easier:

あまりオープンではない言語において、このコードをテストするのは明らかに問題です。しかし、Rubyではこれ以上簡単にはなりえません。


```
Time.stub(:now) { Time.new(2012, 12, 24) }
article.publish!
assert_equal 24, article.published_at.day
```

> This pattern is so useful that Travis Jeffrey wrapped it up in the popular Timecop gem. But if you've acquired a design taste for the Java-friendly pattern of dependency injection, it looks gross. It's a gut reaction trained on pattern matching. The brain goes HARD CODE ALERT! HARD CODE ALERT! That's the danger with patterns: they can quickly graduate from tool to taste.

このパターンは非常に便利なので、Travis Jeffrey氏は人気の高いTimecop gemにまとめています。
しかし、依存性注入というJavaに適したパターンの設計テイストを身につけていると、それは気持ち悪く見えるものです。これは、パターンマッチングで鍛えられた直感的な反応です。脳は「ハードコード・アラート！ハードコード・アラート！」となります。これがパターンの危険なところで、~~ツールからテイストへとすぐに卒業してしまうのです~~ ツールだったはずのものが様式へと変容してしまうのです。[^3]

> Adam Keys' goes on to put forward a fine argument of whether you're designing for your tests or your API (and Lloyd Kupchanko also shared some wise words on how the API suffers from DI). I don't think there's actually much of a dichotomy between the two in a language like Ruby. We can have the clarity and simplicity of hard coded references and still be able to easily test them, as shown above.

> Of course, this runs counter to another Java-derived principle to only mock types you own. Combine that with an affinity of dependency injection and the simplest thing that could possibly work is no longer not just good enough, it's disgusting. It violates the doctrine that has been so carefully assembled.

> That's the real point here: Be careful with who you share your intellectual foundation with. It's fashionable to say "I'm not a Ruby programmer, I'm just a programmer". But languages shape the way we think. While we can cross-pollinate some ideas between languages, there are many we cannot. And worse, the incompatibility is not immediately apparent — especially when they both seem to just be Objective Oriented.

> I'm a Ruby programmer.


Adam Keys氏は、テストとAPIのどちらのために設計しているのかについて、素晴らしい議論を展開しています（Lloyd Kupchanko氏も、APIがいかにDIに苦しんでいるかについて、賢明な言葉を述べています）。Rubyのような言語では、この2つを分ける意味が実際それほどあるとは思えません。上のコードのように、ハードコードされた参照の明快さと単純さを持ちながら、それを簡単にテストすることができます。

もちろん、これは「自分が所有する型だけをモックする」という、Java由来のもう一つの原則に反しています。この原則に依存性注入の親和性を組み合わせると、最もシンプルに動作する可能性のあるものが、もはや十分ではないどころか、最低なものに成り下がります。これまで慎重に組み立てられてきた教義に反するものです。

それがここでの真のポイントです。自分の知的基盤を誰と共有するかに注意してください。
「私はRubyのプログラマーではない。ただのプログラマーです。」と言うのはfashionableですが、言語は私たちの考え方を形成します。
言語間でアイデアを掛け合わせることはできますが、そうできないものもたくさんあります。
さらに悪いことに、互換性のなさはすぐにはわからないものです。特に、どちらもただのオブジェクト指向に見える場合はそうです。

私はRubyのプログラマーです。

## 要約
※私個人による意訳です

- 言語ごとに原理原則は変わるし、何を犠牲にしているかも違う
- なので、ある言語でよいとされているものを異なる言語に盲目的に当てはめるのはやめましょう
- DHHはDI自体を否定しているのではなく、テストのためだけにDIを使うのはRubyにおいては無駄であると言っている
  * 上のコードで言うと、`Time`を外から注入させたい理由がテストのためだけなら「ヤメロ」と
- 誤った依存関係だろうが何だろうが、メソッド内に持っていいということではない
  * 凝集度と結合度は変わらず意識すべき
- Abstract FactoryパターンやStrategyパターンなど、RubyでもDIが実装上有用なケースは普通にある

[^1]: 翻訳ツールかますとメチャクチャだったので、7割マニュアルで訳してます
[^2]: 「そこまでする必要があるか？」という意味っぽい
[^3]: @snowcrushさんにコメントいただき噛み砕いた表現になりました
