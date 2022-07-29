# 負荷試験ツール Gatling

gatling を利用してみる。今までは JMeter を利用していたのですが gatling というツールを試しに使ってみたので感想です。

## インストール

両方とも zip を ダウンロードして実行するだけで簡単に利用開始できる。  
Windows, Linuxともに実行可能 

## 実行環境 Java 8+ が必要

JavaVMで動作するので、JMeterと同じですね。

検証環境は下記の通り。
```
Windows 10 Pro
プロセッサ Intel(R) Core(TM) i5-10210U CPU @ 1.60GHz   2.11 GHz
Memory 16.0 GB (15.8 GB 使用可能)

java -version
openjdk version "11.0.12" 2021-07-20
```

## シナリオ作成について
JMeter : GUI or XML  
Gatling : Scala、Javaで書くことになる。GUIはない（？）  

## Gatlingのシナリオ作成・実行・結果の確認

### シナリオの作成 (java, scala)
user-files/simulation以下にDSL(Javaのプログラム)を配置し実行
※package名を分かりやすものに変更しておく

例: MyBasicSimulation.java 100 userが 10回
```
cat user-files/simulations/MyBasicSimulation.java
/*
 * Copyright 2011-2022 GatlingCorp (https://gatling.io)
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *  http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package mytest;

import static io.gatling.javaapi.core.CoreDsl.*;
import static io.gatling.javaapi.http.HttpDsl.*;

import io.gatling.javaapi.core.*;
import io.gatling.javaapi.http.*;
import java.time.Duration;

public class MyBasicSimulation extends Simulation {

  HttpProtocolBuilder httpProtocol =
      http
          // Here is the root for all relative URLs
          .baseUrl("テストサーバーのURL")
          // Here are the common headers
          .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8")
          .doNotTrackHeader("1")
          .acceptLanguageHeader("en-US,en;q=0.5")
          .acceptEncodingHeader("gzip, deflate")
          .userAgentHeader(
              "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:16.0) Gecko/20100101 Firefox/16.0");

  // A scenario is a chain of requests and pauses
  ScenarioBuilder scn =
      scenario("My Scenario Name").repeat(10).on(
        exec(http("request_1").get("/"))
      );
  {
    setUp(
      scn.injectOpen(        
        atOnceUsers(100)) //
        .protocols(httpProtocol)
    );
  }
}
```

### コマンドラインで実行
クラス名を指定して実行
```
gatling.bat --run-mode local --simulation mytest.MyBasicSimulation
```
※ その他のオプションはヘルプで確認 `gatling.bat -h`
![image](./run_gatling.gif)

### 結果の確認
resultsフォルダ に HTML形式で出力されてブラウザで確認できます。

![image](./Gatling-Stats.gif)

## まとめ
簡単に動かしてみただけでしたが、JMeterより軽く動く気がする。調べてみると「NettyフレームワークとAkkaツールキットを使用しているため高パフォーマンス」とのこと。多くのクライアント数を想定した負荷試験の場合はJMeterより優秀だと思います。
結果を html形式 できれいに表示してくれるのも嬉しいところ。(サイズは6MB程度)
今後しばらくは Gatling を利用してみようと思います。

参考サイト
https://www.baeldung.com/gatling-jmeter-grinder-comparison
