# performance-test

jmeter vs gatling

## install

両方とも zip を ダウンロードして実行するだけで簡単に利用開始できる。  
windwos, linuxともに実行可能  

## 実行環境 Java 8+ が必要
```
Windows 10 Pro
プロセッサ Intel(R) Core(TM) i5-10210U CPU @ 1.60GHz   2.11 GHz
Memory 16.0 GB (15.8 GB 使用可能)
```

```
java -version
openjdk version "11.0.12" 2021-07-20
```

## シナリオ作成
JMeter : GUI or XML  
Gatling : Scala、Javaで書くことになる。GUIはなし？  

## Gatlingのシナリオ作成・実行・結果の確認

### シナリオの作成 (java, scala)
user-files/simulation以下にDSL(Javaのプログラム)を配置し実行
※package名を分かりやすものに変更しておく

例: MyBasicSimulation.java
```
cat user-files/simulations/MyBasicSimulation.java
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
          .baseUrl("https://myreactstorage001.z22.web.core.windows.net")
          // Here are the common headers
          .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8")
          .doNotTrackHeader("1")
          .acceptLanguageHeader("en-US,en;q=0.5")
          .acceptEncodingHeader("gzip, deflate")
          .userAgentHeader(
              "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:16.0) Gecko/20100101 Firefox/16.0");

  // A scenario is a chain of requests and pauses
  ScenarioBuilder scn =
      scenario("My Scenario Name")
          .exec(http("request_1").get("/"))
          .exec(http("request_2").get("/"))
          .exec(http("request_3").get("/"))
          .exec(http("request_4").get("/"))
          .exec(http("request_5").get("/"))
          .exec(http("request_6").get("/"))
          .exec(http("request_7").get("/"))
          .exec(http("request_8").get("/"))
          .exec(http("request_9").get("/"))
          .exec(http("request_10").get("/"));
          //.pause(1)          
          //.pause(Duration.ofMillis(629))          
  {
    setUp(
      scn.injectOpen(        
        atOnceUsers(10)) //
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
簡単なテストであれば大きな違いはない。慣れている方を利用する。
gatlingは 結果をhtml形式できれいに表示してくれる。

