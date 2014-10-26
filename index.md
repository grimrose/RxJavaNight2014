# Vert.xからみたReactive Streams

<a href="https://twitter.com/share" class="twitter-share-button" data-lang="ja" data-hashtags="rxjnight">ツイート</a>
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

<a href="http://b.hatena.ne.jp/entry/http://www.grimrose.org/RxJavaNight2014/" class="hatena-bookmark-button" data-hatena-bookmark-title="Vert.xからみたReactive Streams" data-hatena-bookmark-layout="standard-balloon" data-hatena-bookmark-lang="ja" title="このエントリーをはてなブックマークに追加"><img src="http://b.st-hatena.com/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a><script type="text/javascript" src="http://b.st-hatena.com/js/bookmark_button.js" charset="utf-8" async="async"></script>

## Reactive Streamsとは

[Reactive Streams](http://www.reactive-streams.org/)

### その前にReactive Manifestoについて

[The Reactive Manifesto](http://www.reactivemanifesto.org/)

![](http://www.reactivemanifesto.org/images/reactive-traits.svg)

* Responsive: 応答性
* Resilient: 立ち直りが早い
* Elastic: 柔軟性
* Message Driven: メッセージ駆動

### マイクロサービス化が進む背景について考えてみた

https://gist.github.com/everpeace/c40b69eff6636b9ec2f9#2-reactive-manifesto

> たとえば、[Netflix]のようなサービスを考えてみると、ユーザから常にアクセスにさらされているUI部分のサービスは絶対にとまっちゃならない。なぜならこのUI部分が収益源だから。

> Netflixのメインの画面が表示されないなんてことは一分一秒たりとも起こしたくないはずです。

> マイクロサービスの議論で、悪の権化のように語られているモノリス。

> もしNetflixのWebサービスが、モノリスでできていて、ひとつのコンポーネントとしてデプロイされていたらどうでしょう。

> レコメンデーションエンジン部分にバグがあって、落ちてしまったら？？もう目も当てられないですね。

> でも、レコメンデーションエンジンの部分が独立して動いていれば、UI部分でレコメンデーションエンジンサービスの故障を検知したら、お薦めリストを表示しないというのもできるし、お薦めのデフォルトを呼び出すような事も可能になる。

> こういう依存先サービスの故障を切り離すような機能をサーキットブレカーと呼ぶけれど、現にNetflixはこれをやるために[Hystrix]を作った(もちろんこれだけじゃない)。

> それに、サーキットブレーカの機能はReactive Manifestoでも言及されています。


### Reactive Streamsについて

[非同期ストリーム処理の標準化を目指す "Reactive Streams" とは](http://okapies.hateblo.jp/entry/2014/04/20/212821)


#### SPI - Java interfaces for implementations

ライブラリを作る際は、以下の4つのinterfaceについて実装します。

* Processor
* Publisher
* Subscriber
* Subscription

それぞれのinterfaceの中身は、こちら

```java
public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {

}

public interface Publisher<T> {

    public void subscribe(Subscriber<? super T> s);

}

public interface Subscriber<T> {

    public void onSubscribe(Subscription s);

    public void onNext(T t);

    public void onError(Throwable t);

    public void onComplete();

}

public interface Subscription {

    public void request(long n);

    public void cancel();

}
```

実装する際のRuleは、[こちら](https://github.com/reactive-streams/reactive-streams/blob/master/README.md)。

サンプルは、[こちら](https://github.com/reactive-streams/reactive-streams/tree/master/api/src/examples/java/org/reactivestreams/example/multicast)。


### 実装されたDraft

draft specは、今ところ以下の4つ

* Akka Streams
* Reactor
* RxJava
* Ratpack


#### Akka Streams

[akka meetup] でコミッターの方が発表されたスライドがこちらです。

http://connpass.com/event/8622/

> <iframe src="//www.slideshare.net/slideshow/embed_code/39594175" width="425" height="355" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/ktoso/2014-akkastreamstokyojapanese" title="2014 akka-streams-tokyo-japanese" target="_blank">2014 akka-streams-tokyo-japanese</a> </strong> from <strong><a href="//www.slideshare.net/ktoso" target="_blank">Konrad Malawski</a></strong> </div>


#### Reactor Composable

[Reactor]

[Spring]を開発している[Pivotal]が作ったライブラリ。

使い方については[こちら](http://spring.io/guides/gs/messaging-reactor/)


#### RxJava

[ReactiveX / RxJavaReactiveStreams](https://github.com/ReactiveX/RxJavaReactiveStreams)

[RxJava]と[Reactive Streams]を繋ぐライブラリ。

サンプルは[こちら](https://github.com/ReactiveX/RxJavaReactiveStreams/blob/0.x/src/test/java/rx/RxReactiveStreamsTest.java)。


#### Ratpack

[Ratpack]

Ratpackについては、[Netty], [Vert.x], [RxNetty], [Grails], [Spark], [Dropwizard]との違いが[こちら](http://www.ratpack.io/manual/current/intro.html#compared_to
)に説明されているので、参考にしてください。

RatpackにもStreamsのAPIが用意されています。

http://www.ratpack.io/manual/current/streams.html

http://www.ratpack.io/manual/current/api/ratpack/stream/Streams.html



## Vert.xとは

[Vert.x]

[eclipse/vert.x](https://github.com/eclipse/vert.x)

> Vert.x is a lightweight, high performance application platform for the JVM that's designed for modern mobile, web, and enterprise applications.

概要については、こちらを参照してしてください。

[Vert.x がいいね！（第1回：入門する）](http://acro-engineer.hatenablog.com/entry/2013/08/10/131203)

アーキテクチャについては、こちらを参照してください。

[Vert.xのアーキテクチャ](http://codebreak.com/blog/takezoe/page/671431/)

先ほどの同じNettyで作られている[Ratpack]との違いは何かを、Ratpack側から見た視点で解説されています。

http://www.ratpack.io/manual/current/intro.html#vertx


## Vert.x と RxJava

現在、公式のメンテナーは、以下の方々で、特に[mod-rxvertx]を担当されているのは[@petermd](https://twitter.com/petermd)さんです。

https://github.com/eclipse/vert.x/wiki/Guide-for-module-maintainers

[@petermd](https://twitter.com/petermd)さんは、以下のスライドも公開されています。

> <iframe src="//slides.com/petermd/eclipsecon2014/embed" width="576" height="420" scrolling="no" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>


日本語ですと[@muraken720](https://twitter.com/muraken720)さんの記事があります。

[RxJavaを使ってCallback Hellから脱出する（ Vert.x がいいね！第5回 ）](http://acro-engineer.hatenablog.com/entry/2013/12/06/082444)

[RxJavaを使ってCallback Hellから脱出する（ Java8 ラムダ編 ）](http://acro-engineer.hatenablog.com/entry/2013/12/09/111905)

Vert.xで使われるインスタンスをラップして、Observableなインスタンスにしてくれます。

例として、WebSocketServerにメッセージの送受信を行ってみます。

例の中では、Operatorとして以下のものが利用されています。

* Observable#[create](https://github.com/ReactiveX/RxJava/wiki/Creating-Observables#create)
* Observable#[subscribe](https://github.com/ReactiveX/RxJava/wiki/Observable#onnext-oncompleted-and-onerror)

```java
@Test
public void _web_socket_server() throws Exception {
    // Setup server
    RxHttpServer server = new RxHttpServer(vertx.createHttpServer());
    server.websocket().subscribe(socket ->
                socket.asObservable().subscribe(buffer -> {
                    if ("END".equals(buffer.toString())) {
                        socket.close();
                        return;
                    }
                    socket.writeBinaryFrame(buffer);
                }));
    server.coreHttpServer().listen(8090);

    // Setup client
    HttpClient httpClient = vertx.createHttpClient();
    httpClient.setHost("localhost");
    httpClient.setPort(8090);
    RxHttpClient client = new RxHttpClient(httpClient);

    AtomicInteger count = new AtomicInteger(0);
    client.connectWebsocket("/ws").subscribe((RxWebSocket s) -> {
        // Exercise
        List<String> list = Arrays.asList("foo", "bar", "baz");
        Observable<String> src = Observable.from(list);
        s.writeAsTextFrame(src); // unchecked
        s.write(new Buffer("END"));

        // Verify
        Observable<Buffer> o = s.asObservable(); // unchecked
        o.subscribe(
            // onNext
            buffer -> {
                String message = MessageFormat.format(
                  "{0}: {1}",
                  count.incrementAndGet(), buffer
                );
                System.out.println(message);
            },

            // onError
            (Throwable t) -> fail("error of " + t),

            // onComplete
            () -> {
                assertThat(count.get(), is(3));
                VertxAssert.testComplete();
            });
    });
}

```

EventBusやWebSocketの他にも、Timer, Scheduler等をObservableにしてくれるラッパーが用意されています。


## Vert.x と Promise

[Vert.x]には、[when.js](https://github.com/cujojs/when)をJavaに移植した[englishtown/when.java](https://github.com/englishtown/when.java)を用いた
[englishtown/vertx-mod-when](https://github.com/englishtown/vertx-mod-when)があったり、
その他に、[RxJava]を用いてPromiseの実装をしている[darylteo/rxjava-promises](https://github.com/darylteo/rxjava-promises)を利用したVertx-Promisesがあります。


## Road to 3.0

### [Vert.x 3.0 plan](https://github.com/eclipse/vert.x/wiki/Vert.x-3.0-plan)

### [Vert.x 3.0 API improvements](https://github.com/eclipse/vert.x/wiki/Vert.x-3.0-API-improvements)

現在、Vert.xは3.0に向けて鋭意開発中です。

その中で[Reactive Streams]に対応する予定です。

https://github.com/eclipse/vert.x/wiki/Vert.x-3.0-plan#reactive-streams-support

[Reactive Streams]以外にも、様々な変更や改善が盛り込まれているため、全貌が明らかになるのはかなり先になるかも知れません。


## まとめ

* [Reactive Streams]は、JVMにとって新しい可能性を秘めているプロジェクトである。
* [Vert.x]は、MicroServicesに向いているContainerである。
* [Vert.x]は、[RxJava]と組み合わせることで、より分かりやすく記述することが出来る。
* [Vert.x]に[Reactive Streams]の実装を提供されると、より簡単に他のフレームワークと連携することが出来る。


[Vert.x]: http://vertx.io/
[RxJava]: https://github.com/ReactiveX/RxJava
[Reactive Streams]: http://www.reactive-streams.org/
[akka meetup]: http://connpass.com/event/8622/
[Spring]: https://spring.io/
[Pivotal]: http://www.pivotal.io/
[Reactor]: http://projectreactor.org/
[Ratpack]: http://www.ratpack.io/
[Netty]: http://netty.io/
[RxNetty]: https://github.com/Netflix/RxNetty
[Grails]: http://grails.org/
[Spark]: http://www.sparkjava.com/
[Dropwizard]: https://dropwizard.github.io/dropwizard
[Netflix]: https://www.netflix.com/
[Hystrix]: https://github.com/Netflix/Hystrix/
[mod-rxvertx]: https://github.com/vert-x/mod-rxvertx
