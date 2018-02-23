# M9：リバースエンジニアリング

<table>
 <tr>
  <th>脅威エージェント</th>
  <th>攻撃経路</th>
  <th colspan="2">セキュリティ上の弱点</th>
  <th>技術的な影響</th>
  <th>ビジネスへの影響</th>
 </tr>
 <tr>
  <td align="center" width="20%">アプリ依存 </td>
  <td align="center" width="15%">攻撃難易度<br>容易</td>
  <td align="center" width="15%">蔓延度<br>中</td>
  <td align="center" width="15%">検出難易度<br>容易</td>
  <td align="center" width="17.5%">影響度<br>中</td>
  <td align="center" width="17.5%">アプリ / ビジネス依存</td>
 </tr>
 <tr>
  <td>攻撃者は通常、ターゲットにしたアプリケーションをアプリストアからダウンロードし、様々なツールを使用して、自身のローカル環境で解析します。</td>
  <td>攻撃者は、アプリケーション内に組み込まれたオリジナルの文字列要素、ソースコード、ライブラリ、アルゴリズム、リソースを調査するために、バイナリファイルを解析しようとするでしょう。攻撃者は、自分の環境の中で、IDA Pro、Hopper、otool、stringsなどといった比較的お手頃でよく理解されているバイナリ検査ツールを使用するでしょう。</td>
  <td colspan="2">一般的に、全てのモバイルコードはリバースエンジニアリングの影響を受けやすいです。中には他のアプリケーションと比べてより影響を受けやすいものもあります。実行時に動的なイントロスペクションが許可されている言語/フレームワーク(Java、.NET、Objective C、Swift)で書かれたコードには、特にリバースエンジニアリングのリスクがあります。影響を受けやすいものは、簡単に見抜くことができます。まず初めに、アプリストアバージョンのアプリケーションを復号します(バイナリ暗号化が適用されている場合)。次に、バイナリに対して本ドキュメントの「攻撃経路」で説明されているツールを使用します。もしこれらのツールによって生成される、アプリケーションの制御フローパス、文字列要素、疑似コード/ソースコードがほとんど解読されると、そのコードはこのリスクの影響を受けやすいでしょう。</td>
  <td>攻撃者は、以下のことを達成するために、リバースエンジニアリングを悪用するかもしれません。
   <ul>
    <li> バックエンドサーバの情報の漏えい</li>
    <li> 暗号化用の定数や暗号の漏えい</li>
    <li> 知的財産を窃取</li>
    <li> バックエンドシステムに対する攻撃の実行</li>
    <li> さらなるコード改ざんのための必要な情報の取得</li>
   </ul>
  </td>
  <td>リバースエンジニアリングによるビジネスへの影響は、非常に様々です。以下のようなものが含まれます。
   <ul>
    <li> 知的財産の窃取</li>
    <li> 風評被害</li>
    <li> なりすまし犯罪</li>
    <li> バックエンドシステムへの侵入</li>
   </ul>
  </td>
 </tr>
</table>



## 脆弱性有無の確認
一般的にほとんどのアプリケーションは、コード固有の性質により、リバースエンジニアリングの影響を受けやすいといえます。最近のアプリケーション開発言語は、プログラマがアプリケーションをデバッグしやすくするために、メタデータを多く含んでいます。これにより、攻撃者もアプリケーションの動作を理解しやすくなります。
攻撃者が以下のいずれかを実行できるアプリケーションは、リバースエンジニアリングの影響を受けやすいといわれています。
 - バイナリの文字列要素の内容を明確に理解できる。
 - クロスファンクショナルな解析を正確に実施できる。
 - バイナリからソースコードをかなり正確に復元できる。

ほとんどのアプリケーションはリバースエンジニアリングの影響を受けやすいですが、本リスクを軽減できるかどうか考慮する際には、リバースエンジニアリングによるビジネスへの潜在的な影響を精査することが重要です。 リバースエンジニアリングによって何ができてしまうのか、以下に挙げたいくつかの例をご覧ください。


## 防止方法
リバースエンジニアリングによる悪影響を効果的に防ぐためには、難読化ツールを使用しなければなりません。市場には多くの無料や商用の難読化ツールが存在しています。逆に言えば、市場には多くの難読化解読ツールが存在するということです。選択した難読化ツールの効果を測定するには、IDA ProやHopperのようなツールを使用してコードを解読してみてください。 以下のようなことができると、よい難読化ツールといえます。
 - 難読化するメソッド/コードセグメントを絞り込む
 - パフォーマンスへの影響とバランスを取るために難読化の度合いを調整する
 - IDA ProやHopperといったツールによる難読化解読に耐えうる
 - メソッドだけでなく文字列要素も難読化する


## 攻撃シナリオの例
**シナリオ #1：** 文字列要素解析<br>
攻撃者は暗号化されていないアプリケーションに対して「strings」を実行します。文字列要素を解析すると、攻撃者はバックエンドデータベースの認証資格情報を含むハードコードされた接続用文字列を発見します。攻撃者はこれらの資格情報を使用してデータベースへアクセスできるようになります。攻撃者はアプリケーションのユーザに関する膨大な個人情報を窃取するのです。

**シナリオ #2：** クロスファンクショナル解析<br>
攻撃者は暗号化されていないアプリケーションに対してIDA Proを使用します。文字列要素解析と解析結果を組み合わせた結果、攻撃者はJailbreakを検知するコードを見つけ出します。攻撃者は知りえた情報をその後のコード改ざん攻撃に使用し、モバイルアプリケーション内のJailbreak検知を無効化します。攻撃者は顧客情報を盗むためにmethod swizzlingを悪用したバージョンのアプリケーションをデプロイします。

**シナリオ #3：** ソースコード解析<br>
銀行のAndroidアプリケーションについて考えてみます。APKファイルは、7zip/Winrar/WinZip/Gunzipを使用して簡単に展開することが可能です。一度展開すれば、manifestファイル、アセット、リソース、そして最も重要なclasses.dexファイルを攻撃者は手に入れられます。 その後、Dex to Jarコンバーターを用いて、攻撃者は簡単にjarファイルに変換することができます。そうすれば、Javaデコンパイラ(JDguiのようなもの)によって、コードを取得することができるでしょう。




## 参考資料
### OWASP
 - [OWASP Reverse Engineering and Code Modification Prevention Project](https://www.owasp.org/index.php/OWASP_Reverse_Engineering_and_Code_Modification_Prevention_Project)
 
### 外部リンク
 1. [Arxan Research: State of Security in the App Economy](https://www.arxan.com/assets/1/7/State_of_Security_in_the_App_Economy_Report_Vol._2.pdf), Volume 2, November 2013:
  "Adversaries have hacked 78 percent of the top 100 paid Android and iOS apps in 2013."

 2. [HP Research: HP Research Reveals Nine out of 10 Mobile Applications Vulnerable to Attack](http://www8.hp.com/us/en/hp-news/press-release.html?id=1528865#.UuwZFPZvDi5), 18 November 2013:
 "86 percent of applications tested lacked binary hardening, leaving applications vulnerable to information disclosure, buffer overflows and poor performance. To ensure security throughout the life cycle of the application, it is essential to build in the best security practices from conception."

 3. [North Carolina State University: Dissecting Android Malware: Characterization and Evolution](http://www.csc.ncsu.edu/faculty/jiang/pubs/OAKLAND12.pdf), 7 September 2011:
 "Our results show that 86.0% of them (Android Malware) repackage legitimate apps to include malicious payloads; 36.7% contain platform-level exploits to escalate privilege; 93.0% exhibit the bot-like capability."

 4. [Tech Hive: Apple Pulls Ripoff Apps from its Walled GardenFeb 4th](http://www.techhive.com/article/249310/apple_pulls_ripoff_apps_from_its_walled_garden.html), 2012:
 "While Apple is known for screening apps before they are allowed to sprout up in its walled garden, clearly fake apps do get in. Once they do, getting them out depends on developers who raise a fuss."

 5. [Tech Crunch: Developer Spams Google Play With RipOffs of Well-Known Apps… Again](http://techcrunch.com/2014/01/02/developer-spams-google-play-with-ripoffs-of-well-known-apps-again/), January 2 2014:
 "It's not uncommon to search the Google Play app store and find a number of knock-off or "fake" apps aiming to trick unsuspecting searchers into downloading them over the real thing."

 6. [Extreme Tech: Chinese App Store Offers Pirated iOS Apps Without the Need To Jailbreak](http://www.extremetech.com/mobile/153849-chinese-app-store-offers-pirated-ios-apps-without-the-need-to-jailbreak), April 19 2013:
 "The site offers apps for free that would otherwise cost money, including big-name titles."

 7. [OWASP: Architectural Principles That Prevent Code Modification or Reverse Engineering](https://www.owasp.org/index.php/Architectural_Principles_That_Prevent_Code_Modification_or_Reverse_Engineering), January 11th 2014.

 8. Gartner report: Avoiding Mobile App Development Security Pitfalls, 24 May 2013:
"For critical applications, such as transactional ones and sensitive enterprise applications, hardening should be used."

 9. Gartner report: Emerging Technology Analysis: Mobile Application Shielding, March 26th, 2013:
 "As more regulated and sensitive data applications move to mobile platforms the need to increase data protection increases. Mobile application shielding presents the opportunity to security providers to offer higher data protection standards to mobile platforms that exceed mobile OS security."

 10. Gartner report: Proliferating Mobile Transaction Attack Vectors and What to Do About Them, March 1st, 2013:
 "Use mobile application security testing services and self-defending application utilities to help prevent hacking attempts."

 11. Gartner report: Select a Secure Mobile Wallet for Proximity, March 1st, 2013:
 "Application hardening can fortify sensitive business code against hacking attempts, such as reverse engineering"

 12. Forrester paper: Choose The Right Mobile Development Solutions For Your Organization, May 6th 2013:
 "5 Key Protections: Data Protection, App Compliance, App-Level Threat Defense, Security Policy Enforcement, App Integrity"

 13. [John Wiley and Sons, Inc: iOS Hacker's Handbook](http://www.amazon.com/iOS-Hackers-Handbook-Charlie-Miller/dp/1118204123), Published May 2012, [ISBN 1118204123](https://www.owasp.org/index.php/Special:BookSources/1118204123).

 14. [McGraw Hill Education: Mobile Hacking Exposed](http://mobilehackingexposed.com/), Published July 2013, [ISBN 0071817018](https://www.owasp.org/index.php/Special:BookSources/0071817018).

 15. [Publisher Unannounced: Android Hacker's Handbook](http://www.amazon.com/Android-Hackers-Handbook-Joshua-Drake/dp/111860864X), To Be Published April 2014.

 16. [Software Development Times: More than 5,000 apps in the Google Play Store are copied APKs, or 'thief-ware'](http://sdt.bz/66393#ixzz2sHa7dFMp), November 20 2013:
 "In most cases, the 2,140 copycat developers that were found reassembled the apps almost identically, adding new advertising SDKs to siphon profits away from the original developers.

 17. [InfoSecurity Magazine: Two Thirds of Personal Banking Apps Found Full of Vulnerabilities](http://www.infosecurity-magazine.com/view/36376/two-thirds-of-personal-banking-apps-found-full-of-vulnerabilities/), January 3 2014:
 "But one of his more worrying findings came from disassembling the apps themselves ... what he found was hardcoded development credentials within the code. An attacker could gain access to the development infrastructure of the bank and infest the application with malware causing a massive infection for all of the application's users."

 18. [InfoSecurity Magazine: Mobile Malware Infects Millions; LTE Spurs Growth](http://www.infosecurity-magazine.com/view/36686/mobile-malware-infects-millions-lte-spurs-growth/), January 29 2014:
 "Number of mobile malware samples is growing at a rapid clip, increasing by 20-fold in 2013... It is trivial for an attacker to hijack a legitimate Android application, inject malware into it and redistribute it for consumption. There are now binder kits available that will allow an attacker to automatically inject malware into an existing application"
