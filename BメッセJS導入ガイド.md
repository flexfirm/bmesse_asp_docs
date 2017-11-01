# BメッセJS導入ガイド
本ガイドは、チャット用ASP製品「Bメッセ」の利用にあたり、提供する各種API、設定の方法に関して記載しています。

## 目次
[Bメッセとは](#Bメッセとは)  
[動作環境](#動作環境)  
[インストール](#インストール)  
[使用方法（オペレーター）](#使用方法オペレーター)  
[使用方法（カスタマー）](#使用方法カスタマー)  
[その他設定](#その他設定)  
[用語](#用語)  
[変更履歴](#変更履歴)  
[ライブラリの利用](#ライブラリの利用)  

<h2 id="Bメッセとは">Bメッセとは</h2>

![Bメッセ画像](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/bmesse_image.JPG)

Bメッセはメッセージング機能を開発するための基盤です。  
ASPサービスとして提供するWebコンソールにアクセスしても利用でき、また既存のWebアプリケーション内に組み込んでも利用できます。  
主に以下のような機能を開発するために役立ちます。  
・リアルタイムチャット機能の開発  
・チャットボットの開発  
・コミュニケーションの統合  
（LINE・Webチャット・メール・Facebookといったあらゆるコミュニケーションを１つのタイムラインで送受信・表示を可能にする）  
これらを可能にするチャット用のASP製品です。  

<h2 id="動作環境">動作環境</h2>

オペレーターが利用するWebコンソールおよびカスタマーが利用するWebチャットのサポート対象環境は下記の通りです。  
LINEの利用においてはLINEのサポート対象環境に依存します。

- Windows7
	- Google Chrome 最新版
	- Internet Explorer (v11以降）
- Windows10
	- Google Chrome 最新版
	- Microsoft Edge 最新版
- Mac OSX
	- Google Chrome 最新版
- iOS
	- Safari のみ
	- iOS9 以上
	- iPhone5 以上または iPad(3rd) 以上または iPad mini(2nd) 以上
- Android
	- Chrome のみ
	- Android5.1 以上

<h2 id="インストール">インストール</h2>
Bメッセを使用するページのhtmlファイルに以下のタグを入れてください  

**※ `bmesse-config.js`を読み込んだ後に、`bmesse.js`を読み込む必要があります。**

```html
<link rel="stylesheet" href="https://asp.bmesse.com/libs/{バージョン番号}/bmesse.css">
<script type="text/javascript" src="{bmesse-config.jsへのパス}"></script>
<script src="https://asp.bmesse.com/libs/{バージョン番号}/bmesse.js"></script>
```

bmesse.cssをカスタマイズする場合、上記の`bmesse.css`のコピーファイルをカスタマイズし、その保存先を指定するようにして下さい。  
```html
<link rel="stylesheet" href="{bmesse.cssへのパス}">
```

### bmesse-config.jsの編集  

**Bmesse.WEBCHAT_COMMUNICATION_ENABLE**  
BメッセからWebチャットへの送受信を有効にします。有効にする場合は`true`にします。

**Bmesse.LINE_COMMUNICATION_ENABLE**  
BメッセからLINEへの送受信を有効にします。有効にする場合は`true`にします。(※)  

**Bmesse.MAIL_COMMUNICATION_ENABLE**  
Bメッセからメールへの送受信を有効にします。有効にする場合は`true`にします。(※)  

**Bmesse.FACEBOOK_COMMUNICATION_ENABLE**  
BメッセからFacebookへの送受信を有効にします。有効にする場合は`true`にします。(※)  
(※)開発中の機能です  

**Bmesse.MESSAGE_DELETE_ENABLE**  
メッセージを削除可能に設定します。有効にする場合`true`にします。  
(この機能はカスタマー用のチャット窓のみで利用可能です)  

<h2 id="使用方法オペレーター">使用方法（オペレーター）</h2>
オペレーター用のWebチャットは、LINEとWebチャットとメールを1つのタイムラインで表示することができるため、カスタマーとのメッセージのやり取りを効率的に管理することができます。  
それでは、具体的な使用方法を説明します。  

### Bメッセの初期化と表示

Bメッセを使用するために必ず実行してください。  
具体的には以下のように実行します。   

```javascript
var bmsClient = new BmesseClient();
bmsClient.initIntegrationUser(integrationUserId, 
    authToken,
    /* completeCallback */ function(error) {
        if (error) {
            console.error('初期化でエラーが発生しました。');
        }
        else {
            // メッセージを指定してチャット窓を表示する
            bmsClient.showFrameWithMessageId(selector, messageId, title);
        }
    },
    /* stateChangedCallback */ function() {
        console.info('認証状態が変更になりました。');
    }
);
```  

- initIntegrationUser()
    - __`integrationUserId`__： オペレーターを一意に認識できるコードを利用します。例えば従業員IDなどを利用してください。  
    - __`authToken`__： 導入したWebシステムの認証処理において発行されたBメッセ用の認証トークン。  

    - __`completeCallback`__： 成功またはエラーによる処理の完了時のコールバック。有効期限切れ等、無効な認証トークンを指定した場合は error オブジェクトが渡される。  
    - __`stateChangedCallback`__： Bメッセの認証状態が変更になった際に呼び出されるコールバック。現在は未使用。  

- showFrameWithMessageId()
    - __`selector`__: チャット窓をはめ込む箇所の、htmlタグid属性を指定します。  
 例えば 、チャット窓をはめ込みたい場所が`<div id="messages_container"></div>`だった場合、  
 `selector`の部分に`'#messages_container'`を渡します。  
    - __`messageId`__： Bメッセにて発行された、表示したいメッセージのメッセージIDを渡します。  
    - __`title`__： チャット窓のヘッダー名として表示されます。  

### Bメッセのインスタンスを取得する

Bメッセの主要機能にアクセスするために、下記メソッドでBメッセのインスタンスを取得します。
```javascript
var bmesse = bmsClient.getBmesse();
```

### オペレーターのオンライン・オフライン状態の制御
#### オンラインにする
オペレーターをチャット可能な状態にするためにこのメソッドを呼びます。  
なお、このメソッドを使うことで相手側のチャット窓でオンラインを示すランプが点灯します。  

```javascript
bmesse.onLine();
```
#### オフラインにする
オペレーターがチャットを受け付けないようにするために、このメソッドを呼びます。  
相手側のチャット窓で、オンラインを示すランプが消灯します。  

```javascript
bmesse.offLine();
```
表示中のページが更新される時にオフラインにする制御はSDK内では行っていません。  
必要に応じて呼び出してください。

##### 相手ユーザーから見えるユーザーの状態

| オンライン時 | オフライン or 不明時 |
|:------:|:------:|
| ![オンライン](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/online.PNG) |![オフライン](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/offline.png)  |

※デフォルトは「オフライン」状態です。  
※状態を取得できるのは、相手がWebチャットを利用しているときのみです。  

```javascript
bmsClient.showFrameWithCustomerId = function(selector, groupKey, integrationUserId, title)
```
- __`selector`__:　チャット窓をはめ込む箇所の、htmlタグid属性を指定します。  
　例えば　、チャット窓をはめ込みたい場所が`<div id="messages_container"></div>`だった場合、  
　`selector`の部分に`'#messages_container'`を渡します。  
##### チャット相手のカスタマーを指定して表示する
チャット相手のユーザーIDを指定して表示する場合は以下メソッドを使用します。

```javascript
bmsClient.showPopupWithCustomerId(integrationUserId, title);
```
__`integrationUserId`__： 統合したアプリ側のカスタマーユーザーIDを渡します。  
__`title`__： チャット窓のヘッダー名として表示されます。  
##### チャットのグループを指定して表示する
チャットグループを指定して表示する場合は以下メソッドを使用します。グループに対応するスレッドIDを指定します。

```javascript
bmsClient.showPopupWithThreadId(threadId, title);
```
__`threadId`__： スレッドIDを渡します。  
__`title`__： チャット窓のヘッダー名として表示されます。  

- __`groupKey`__： 店舗担当者が所属する加盟店コード。
- __`integrationUserId`__： メッセージの送信先となる顧客コード  
- __`title`__： チャット窓のヘッダー表示するタイトル  

## Bメッセに機能を割り当てる
いくつかのパーツに機能を割り当てることができます。  

### [+]ボタン

| 未使用時 | [+]ボタン使用時 |
|:------:|:------:|
| ![未使用](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/un_attach_plus_button.JPG) |![使用](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/attach_plus_button.JPG)
  |

`attachToPlusButton(yourFunction)`関数を使うことで、
メッセージ入力テキストの左側に[+]ボタンを設置できます。  
以下のように定義することで、[+]ボタンクリック時に任意の処理を実行できます。  
なお、この関数はBメッセを表示する前に一度だけ実行しておく必要があります。

```javascript
bmesse.attachToPlusButton(
    function(){
	    //処理
    }
);
//Bメッセを表示
bmsClient.showFrameWithMessageId(selector, messageId, title);
```
引数に連想配列を渡してあげることで、最大４つまで機能をメニューに登録することができます。  

| メニュー非表示時 | メニュー表示時 |
|:------:|:------:|
| ![メニュー非表示時](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/attach_plus_button_close.JPG) |![メニュー表示時](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/attach_plus_button_open.JPG)
  |

連想配列は以下の項目を定義する必要があります。  

| キー		  | 必須 	 | 説明 |
| :--			| :--:	 | :--- |
| `function`	|✔		| 実行したい処理の関数|
| `icon`		|✔		| アイコン用のSVGコード|
| `functionName`|		 | アイコンに表示する文字列|

```javascript
//割り当てたい機能を定義
var functionsAssociativeArray = [
    {
		//アイコンクリック時に実行させたい関数
		"function":function(){処理}
		//アイコン用svgコード
		"icon":'<svg height="100" width="100"><circle cx="50" cy="50" r="40" stroke="black" stroke-width="3" fill="red" /></svg>',
		//アイコンの下に表示する文字列
		"functionName":'テンプレートエディタ',
	},
	...//他に３つまで追加可能
];
//[+]に割り当てたい機能をセット
bmesse.attachToPlusButton(functionsAssociativeArray);
//Bメッセを表示
bmsClient.showFrameWithMessageId(selector, messageId, title);
```

### メールの[作成]ボタンクリック時
※開発中の機能です  
メールをプラットフォームとして選択している場合は、テキストボックスの[送信]ボタンはにメールの[作成]ボタンとなります。  
これは、メールエディタを開いて、メールを作成することを想定しています。

| メール以外を選択時 | メール選択時 |
|:------:|:------:|
| ![メール以外を選択時](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/send_button.JPG) |![メール選択時](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/build_mailer_button.JPG) |
  

[作成]ボタンには`bmesse.attachToBuildMailerButton(yourFunction)`関数で処理を定義しておく必要があります。  
また、[作成]ボタンでプレビューと送信を行う場合は、`HtmlEditor`オブジェクトの`render(z-index)`メソッドを呼んでおく必要があります。  
プレビュー時にビューや処理を追加するには、事前に`HtmlEditor`オブジェクトに対して処理を登録しておく必要があります。  
以下実装例です。

```javascript
bmesse.attachToBuildMailerButton(function(){
    //HTMLメールエディタの設定処理
    var htmlEditor = new Bmesse.HtmlEditor('HTMLメールエディタ');
    
    // メールアドレス検索処理の実装
    var mailSearchFunction = function(decisionFunction) {
        decisionFunction('ぶんしろう', 'bunshirou@bmesse.com');
    }

    //[To][Cc][Bcc]ボタンにメールアドレス検索機能をアタッチ
    htmlEditor.attachFunctionToImportToButton(mailSearchFunction);
    htmlEditor.attachFunctionToImportCcButton(mailSearchFunction);
    htmlEditor.attachFunctionToImportBccButton(mailSearchFunction);
  
      //一時保存ボタン
    htmlEditor.attachFunctionToCommit1Button('一時保存', function(editor) {
        // 一時保存処理の実装
        var content = editor.getContentText();
        $('#save-content').text(content);
    });

    //送信ボタン
    htmlEditor.attachFunctionToCommit2Button('送信', function(editor) {
        // 入力チェックを実行し、エラーの際は送信しない。
        if (! editor.validateEmail()) {
            return;
        }
        // 送信処理の実装
        var content = editor.getContentText();
        console.log(content);
    });

    //カスタムViewをアタッチ
    htmlEditor.attachCustomizeViewToSide('<div><input id="html-editor-test-add-text" type="button" value="テキストを追加" /></div>', 30);

    //カスタムViewに処理をアタッチ
    htmlEditor.attachFunctionToCustomizeView(function() {
        $('#html-editor-test-add-text').click(function() {
                htmlEditor.appendContentText('ほげほげ<br />');
        });
    });
    
    htmlEditor.render(1300);
});
```

### メールの[返信]ボタンクリック時
※開発中の機能です  

| タイムライン画面 ＞ | ＜ メールプレビュー画面 |
|:------:|:------:|
| ![タイムライン画面](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/bms_timeline.JPG) |![メール詳細画面](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/bms_mail_detail.JPG) |
  
メールの[返信]ボタンには、`bmesse.attachMailReplyButton(yourFunction)`関数で処理を定義しておく必要があります。  
なお、[返信]ボタンクリック時は、メールエディタを起動することを想定していますが、このときに返信先のアドレスや引用文の挿入など設定しておくことができます。  
以下実装例です。  

```javascript
bmesse.attachMailReplyButton(function(messageObj){
    //HTMLメールエディタの設定処理
    var htmlEditor = new Bmesse.HtmlEditor('HTMLメールエディタ');
    
    // ～各種設定～
    // attachToBuildMailerButtonの処理を参照のこと

    //返信でエディタを起動するときの初期値を設定
    if(messageObj){
        //Toアドレスがあれば
        var toAddressArray = [];
        if(messageObj.getToAddresses()[0]){
            //その数分だけToアドレスをセット
            messageObj.getToAddresses().forEach(function(val){
                toAddressArray.push(val['address']);
            });
            htmlEditor.setToEmails(toAddressArray);
        }
        
        //全員返信の場合はCcアドレスもセット
        //var ccAddressArray = [];
        //if(messageObj.getCcAddresses()[0]){
        //    messageObj.getCcAddresses().forEach(function(val){
        //        ccAddressArray.push(val['address']);
        //    });
        //    htmlEditor.setCcEmails(ccAddressArray);
        //}

        //件名
        htmlEditor.setSubject(messageObj.getSubject());

        //本文
        htmlEditor.setContentText(messageObj.message);
    }

    htmlEditor.render(1300);
});
```

### メールの[全員返信]ボタンクリック時
※開発中の機能です  

メールの[全員返信]ボタンには、`bmesse.attachMailReplyAllButton(yourFunction)`関数で処理を定義しておく必要があります。  
実装方法は、**メールの[返信]ボタンクリック時**をご参照ください。  

### メールの[転送]ボタンクリック時
※開発中の機能です  

メールの[転送]ボタンには、`bmesse.attachMailForwardButton(yourFunction)`関数で処理を定義しておく必要があります。  
実装方法は、**メールの[返信]ボタンクリック時**をご参照ください。  

## メッセージ一覧を取得する
※開発中の機能です  

グループキー（グループを識別するコード。例えば店舗IDなど）とカスタマーのユーザー識別子を指定して、そのユーザーの新着メッセージ受信イベントを登録します。  
画面上にアクティブメッセージの内容を反映する場合などに利用してください。  
以下実装例です。  

```javascript
bmesseClient.addListenerMessageAddedWithCustomerId(groupKey, integrationUserId, timestamp, function(messageModel) {
	// 画面上の要素に受信メッセージを表示する
	$('#' + messageModel.id).text(messageModel.message);
});
```
- __`groupKey`__： 店舗担当者が所属する加盟店コード。
- __`integrationUserId`__： 統合したアプリケーション側のチャット相手とするユーザの識別子  
- __`timestamp`__： このタイムスタンプ以降のメッセージを取得する。UNIX時刻  
- __`callbackFunction`__： メッセージ受信時に呼び出されるコールバック関数。引数はメッセージモデル  

## 店舗全体で、新着メッセージのあるユーザー数を表示する

グループキーを指定して、店舗ユーザーが閲覧していないメッセージの数を取得します。  
このメソッドは新着メッセージが来るたび反応するため、既に新着のあるユーザーをカウントしない場合は、以下のように実装します。  

```javascript
bmesseClient.addListenerMessageAddedWithGroupKey(groupKey, function(messageModel) {
    // 新着ユーザーでない場合
    if($('#' + messageModel.threadId + ' .message-mark').text()!='new'){
        // 店舗全体の新着スレッド数カウントアップ
        $('#notification-counter').text(notificationThreadCounter++);
    }
    ///ユーザーに新着マークをつける
    $('#' + messageModel.threadId + ' .message-mark').text('new');
});
```
- __`groupKey`__： 店舗担当者が所属する加盟店コード。
- __`callbackFunction`__： メッセージ追加時のコールバック関数。引数はそのスレッド内の最新メッセージモデル  

## ユーザーの状態を受け取る
例えばWebカスタマーで、以下のようにユーザー一覧の表示する場合、ユーザーの「状態」やメッセージの「新着」などの情報を、Webカスタマー内に表示する必要があります（赤枠）  
そのような場合について説明します。  

![img](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/app_sample.jpg)

### ユーザー一覧を取得する

```javascript
// 全ユーザーからの新着メッセージを取得し
var arrival = new Bmesse.Arrival();

// 新たな新着メッセージを受信したときにリアルタイムに検知する
arrival.addListenerArrivalAdded(function (arrivalModel) {
	//（１）ユーザーのIDを取得
	var userId = arrivalModel.fromUserId;

	//（２）ユーザーの状態を取得（オンライン・オフライン状態などのアプリにおける状態）
	var systemUserStatusModel = arrivalModel.getSystemUserStatus();

	//（３）ユーザーの状態を取得（チャット中、書込み中、退出中などのチャット窓における状態）
	var threadUserStatusModel = arrivalModel.getThreadUserStatus();

	//（４）受信したメッセージを取得
	var messageModel = arrivalModel.getMessage();
});
```

上記の`function(userInfo){処理}`の部分をご覧ください。  
この`{処理}`の部分は、オペレーター（Webユーザー）が所属するグループ（店舗）に対して送信されたカスタマーからのメッセージに対してイベントが発生します。  

なお、`{処理}`内で取得できる（１）～（４）の詳細は以下です。  

|     |取得できる値やオブジェクト |利用可能なプラットフォーム |
|:------:|:------:|:------:|
| （１） |user_id（文字列）|全て|
| （２） |Bmesse.SystemUserStatusオブジェクト|Webチャットのみ|
| （３） |Bmesse.ThreadUserStatusオブジェクト|Webチャットのみ|
| （４） |Bmesse.Messageオブジェクト|全て|

それでは（２）～（４）の具体的な使用方法を説明します。  

### ユーザーの情報を表示する
あなたのサービスのDBからデータを取得して、ユーザーの情報を表示したい場合に、この（１）ユーザーのIDを使用ください。  

### ユーザーのオンライン、オフライン状態をリアルタイムに受け取る
**※この機能は、相手がWebチャットを利用しているときのみ利用可能です。**  

（２）は以下のように使用します。  

```javascript
//（２）ユーザーの状態を取得（オンライン・オフライン状態などのアプリにおける状態）
var systemUserStatus = arrivalModel.getSystemUserStatus();
//（２）の状態変化をリアルタイムに検知する
systemUserStatus.addListenerStatus(customerUserId, function(_systemUserStatus) {

	//（２）-1 ユーザーのIDを取得
	var userId = _systemUserStatus.userId;
	//（２）-2 変化したユーザーの状態を取得（オンライン・オフライン状態などのアプリにおける状態）
	var userStatus = _systemUserStatus.status;

	//これ以降にあなたの行いたい処理を記述します。例えば以下のように記述します。
	if (userStatus == Bmesse.SystemUserStatus.ONLINE){
		// 例）このユーザーの状態を「オンライン」と表示する処理
	}else{
		// 例）このユーザーの状態を「オフライン」と表示する処理
	}
});
```
`function (systemUserStatus){処理}`の部分をご覧ください。  
この`{処理}`の部分は、ユーザーの状態が変化するたびに発生します。  

なお、`{処理}`内で取得できるのは`SystemUserStatus`オブジェクトで、  
取得できる値としては上記コードに記載した（２）1～2に示した値です。  

__（２） - 2 取得できる状態の値__  

|状態     |Bメッセでの定数 (int) |
|:------:|:------:|
| オンライン |Bmesse.SystemUserStatus.ONLINE|
| オフライン |なし|

### ユーザーのチャット窓での状態をリアルタイムに受け取る
**※この機能は、相手がWebチャットを利用しているときのみ利用可能です。**  

（３）は以下のように使用します。  

```javascript
//（３）ユーザーの状態を取得（チャット中、書込み中、退出中などのチャット窓における状態）
var threadUserStatus = arrivalModel.getThreadUserStatus();

threadUserStatus.addListenerStatus(customerUserId, function(_threadUserStatus) {

	//（３）-1 ユーザーとのチャット窓のIDを取得
	var threadId = _threadUserStatus.threadId;
	//（３）-2 ユーザーのIDを取得
	var userId = _threadUserStatus.userId;
	//（３）-3 変化したユーザーの状態を取得（チャット中、書込み中、退出中など）
	var userStatus = _threadUserStatus.status;

	//これ以降にあなたの行いたい処理を記述します。例えば以下のように記述します。
	switch (userStatus){
	case Bmesse.ThreadUserStatus.INACTIVE:
		//例）未使用のため、ACTIVE時と同じ処理をする
	case Bmesse.ThreadUserStatus.ACTIVE:
		//例）このユーザーの状態を「チャット中」と表示する処理
		break;
	case Bmesse.ThreadUserStatus.WRITING:
		//例）このユーザーの状態を「書込み中」と表示する処理
		break;
	default:
		//例）このユーザーの状態を「退出中」と表示する処理
		break;
	}
});

```
`function (threadUserStatus){処理}`の部分をご覧ください。  
この`{処理}`の部分は、ユーザーの状態が変化するたびに発生します。  
なお、`{処理}`内で取得できるのは`ThreadUserStatus`オブジェクトで、  
取得できる値としては上記コードに記載した（３）1～3に示した値です。  

__（３） - 3 取得できる状態の詳細__  

|状態                |Bメッセでの定数 (int) |
|:-----------------:|:------:|
| チャット中            |Bmesse.ThreadUserStatus.ACTIVE|
| (ユーザーは未使用) |Bmesse.ThreadUserStatus.INACTIVE|
| 書込み中            |Bmesse.ThreadUserStatus.WRITING|
| 退出中             |なし|

### ユーザーからの新着メッセージの情報を受け取る
（４）は以下のように使用します。  

```javascript
//（４）ユーザーと、自分との間でやりとりした最後のメッセージ情報を取得
var latestMessage = arrivalModel.getMessage();
latestMessage.isRead(myUserId,function (callbackIsRead, newMessage) {
	//これ以降にあなたの行いたい処理を記述します。例えば以下のように記述します。
	if(!callbackIsRead){
		//例）ユーザーの「新着」マークや、新着メッセージの内容を画面ロード時に表示する処理
	}
});
```
`function (callbackIsRead, newMessage) {処理}`で受け取れるのは`Message`オブジェクトです。  
取得できる値は以下の通りです。

__新着を判別する値__  
`callbackIsRead`

|状態                |値 (論理値)   |
|:-----------------:|:------:|
| 既読            |true       |
| 未読            |false       |


### スレッドの状態をリアルタイムに検知する

**※この機能は、相手がWebチャットを利用しているときのみ利用可能です。**  

チャットを行うスレッドは状態を持っており、リアルタイムに検知することができます。  
状態の詳細は **(３)- 3 取得できる状態の詳細**  をご覧ください。

```javascript
// スレッドの状態変更をリアルタイムに検知する
var myThread = bmesse.getThread();
myThread.addListenerStatusChanged(function(status){
    concole.log('スレッドのステータスが'+status+'に変更されました');
});
```

### 選択されている送信先プラットフォームを取得する
※開発中の機能です  

`bmesse`オブジェクトの`getSelectedPlatform`メソッドBメッセで選択されている送信先プラットフォームを取得することができます。  
取得できる値は以下です。  

|プラットフォーム	|Bメッセでの定数 (int)  |値  |
|:-----				|:----- |:----- |
| 未選択				|  |null|
| LINE				|Bmesse.Message.PLATFORM_TYPE_LINE  |0|
| Webチャット		|Bmesse.Message.PLATFORM_TYPE_WEB  |1|
| メール			 |Bmesse.Message.PLATFORM_TYPE_MAIL  |2|
| Facebook			|Bmesse.Message.PLATFORM_TYPE_FACEBOOK  |3|

たとえば以下のように利用します
```javascript
var nowSelected = bmesse.getSelectedPlatform();
//メールが選択されている場合の処理
if(nowSelected == Bmesse.Message.PLATFORM_TYPE_MAIL){
	bmesse.previewHtmlMailForSend(
		//処理
	);
}
//LINEやWebチャットが選択されている場合の処理
else{
	bmesse.previewTemplateForSend(
		//処理
	);
}
```

## HTMLメールやテンプレートの利用
※開発中の機能です  

Bメッセでは、HTMLメールやテンプレートを利用して、リッチな文章を送信することができます。  
HTMLメールエディタや、テンプレートエディタには、カスタムビューを追加することもできます。  

### HTMLメールエディタを利用する
`htmlEditor`オブジェクトの`render(z-index)`メソッドを利用することで、HTMLメールエディタを起動することができます。  
詳細はBメッセのAPIリファレンスを参照してください。  


### テンプレートエディタを利用する
`TemplateEditor`オブジェクトの`render(z-index)`メソッドを利用することで、テンプレートエディタを起動することができます。  
詳細はBメッセのAPIリファレンスを参照してください。  

#### テンプレートJSONのルール
テンプレートで利用できるメッセージは、テキスト、画像、ファイル添付、ボタンのいずれかです。  
テンプレートはjson配列で定義します。  
配列内の定義方法は[LINEのMessagingAPI](https://devdocs.line.me/ja/#template-message)に準拠していますが、ここに記載したもの以外は利用できません。  
また、LINEのPC用のLINEアプリでは、ボタンUIの表示はできません(2017/6現在)  

#### テキスト
```json
[{
	"type":"text",
	"text": "ここにテキストを入力します"
}]
```
上記は以下のようにプレビューされます。

| LINEとWebチャット |
|:------:|
|![LINEプレビュー](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/templ_editor_text.JPG)|

#### 画像
```json
[{
	"type": "image",
	"previewImageUrl": "https://www.***/smallimg_123.jpg",
	"originalContentUrl": "https://www.***/img_123.jpg"
}]
```
※urlはhttpsであることと、画像はjpgかpngであることが必要です。  
※Bメッセでは画像の情報は`originalContentUrl`のみ利用しますが、LINEではどちらも利用するためどちらも必須項目です。LINEの仕様についての詳細は[こちら](https://devdocs.line.me/ja/#send-message-object)をご覧ください。  
上記は以下のようにプレビューされます。

| LINEとWebチャット |
|:------:|
|![LINE画像プレビュー](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/templ_editor_img.JPG)|

#### ボタン
```json
[{
    "type": "template",
    "altText": "thisisabuttonstemplate",
    "template": {
        "type": "buttons",
        "text": "解決しましたか？",
        "actions": [
            {
                "type": "message",
                "label": "解決しました",
                "text": "解決しました",
            },
            {
                "type": "message",
                "label": "いいえ解決していません",
                "text": "いいえ解決していません",
            }
        ]
    }
}]
```
上記は以下のようにプレビューされます。

| LINEとWebチャット |
|:------:|
|![LINE画像プレビュー](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/templ_editor_button.JPG)|

なお、最大４つまでボタンを設定することができます。  

※Bメッセでは`label`は利用していませんが、LINEでは必須項目です。  
LINEでは、吹き出し内のボタンテキストに`label`を、ボタンを押した時に送信されるテキストに`text`が利用されています。  
LINEの仕様についての詳細は[こちら](https://devdocs.line.me/ja/#template-message)をご覧ください。  


#### ファイル
```json
[{
    "type": "template",
    "altText": "thisisabuttonstemplate",
    "template": {
        "type": "buttons",
        "text": "重要事項説明書.zip",
        "actions": [
            {
                "type": "uri",
                "label": "表示する",
                "uri": "http://example.com/page/重要事項説明書.zip"
            }
        ]
    }
}]
```
`template>text`はファイル名を記載します。  
※Bメッセでは`altText`は利用していませんが、LINEでは必須項目です。  
LINEでのファイル添付はTemplate Messageによってこれを実現しています。  
LINEの仕様についての詳細は[こちら](https://devdocs.line.me/ja/#template-message)をご覧ください。  
表示のされ方は、ボタンのプレビューと同じようになります。  

#### 組み合わせてテンプレートを作成する
例えばテキストと画像を組み合わせる場合、以下のように定義することができます。
このように組み合わせてjsonを定義することで、いろいろなタイプのテンプレートを作成できます。
```json
[{
    "type":"text",
    "text": "ページヘッダーです"
},
{
    "type": "image",
    "previewImageUrl": "https://www.***/smallimg_123.jpg",
    "originalContentUrl": "https://www.***/img_123.jpg"
},
{
    "type":"text",
    "text": "ページフッターです"
}]
```

## チャット窓の外観を変更する
cssで変更できる部分のみ外観を変更することができます。
下記のように、元あったcssの代わりに新たなcssファイルを読み込んでください

```html
<!-- <link rel="stylesheet" type="text/css" href="{bmesse-バージョン番号.cssへのパス}"> -->
<link rel="stylesheet" type="text/css" href="{新たな.cssへのパス}">
```
※なお、Bメッセの高さを変更するには`#bms_messages`の`height`の値を上書きします。  

詳細はファイルのコメントを直接ご覧ください。  
<h2 id="使用方法カスタマー">使用方法（カスタマー）</h2>

カスタマーに利用してもらうためのWebチャットは、オペレーター用のように多機能ではありません。  
LINEもメールも使えませんが、そのためシンプルで迷わずすぐに使えるのが特徴です。  
それでは、具体的な使用方法を説明します。  

### Bメッセの初期化と表示

Bメッセを使用するために必ず実行してください。  
具体的には以下のように実行します。   

```javascript
var bmsClient = new BmesseClient();
bmsClient.initIntegrationUser(integrationUserId, 
    authToken,
    /* completeCallback */ function(error) {
        if (error) {
            console.error('初期化でエラーが発生しました。');
        }
        else {
            // チャット窓の表示
            bmsClient.showCustomerFrameWithGroupKey(selector, groupKey, title);
        }
    },
    /* stateChangedCallback */ function() {
        console.info('認証状態が変更になりました。');
    }
);
```  

- initIntegrationUser()
    - __`integrationUserId`__： システムにログインしているカスタマーの顧客コード  
    - __`authToken`__： 導入したWebシステムの認証処理において発行されたBメッセ用の認証トークン。  
（※取得方法は「BメッセRESTAPIリファレンス.md」を参照してください。）  
    - __`completeCallback`__： 成功またはエラーによる処理の完了時のコールバック。有効期限切れ等、無効な認証トークンを指定した場合は error オブジェクトが渡される。  
    - __`stateChangedCallback`__： Bメッセの認証状態が変更になった際に呼び出されるコールバック。現在は未使用。  

- showCustomerFrameWithGroupKey()
    - __`selector`__： チャット窓と差し換える要素のセレクタ。IDを指定。  
    - __`groupKey`__： メッセージ送信先となる加盟店コード  
    - __`title`__： Bメッセに表示するタイトル  

### Bメッセのインスタンスを取得する

Bメッセの主要機能にアクセスするために、下記メソッドでBメッセのインスタンスを取得します。
```javascript
var bmesse = bmsClient.getBmesse();
```

### カスタマーのオンライン・オフライン状態の制御
#### オンラインにする
このメソッドを使うことで相手側のチャット窓でオンラインを示すランプが点灯します。  

```javascript
bmesse.onLine();
```
#### オフラインにする
このメソッドを使うことで相手側のチャット窓でオンラインを示すランプが消灯します。  

```javascript
bmesse.offLine();
```
表示中のページが更新される時にオフラインにする制御はSDK内では行っていません。  
必要に応じて呼び出してください。

## Bメッセに機能を割り当てる

### [+]ボタン

| 未使用時 | [+]ボタン使用時 |
|:------:|:------:|
| ![未使用](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/un_attach_plus_button.JPG) |![使用](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/attach_plus_button.JPG)
  |

`attachToPlusButton(yourFunction)`関数を使うことで、
メッセージ入力テキストの左側に[+]ボタンを設置できます。  
以下のように定義することで、[+]ボタンクリック時に任意の処理を実行できます。  
なお、この関数はBメッセを表示する前に一度だけ実行しておく必要があります。

```javascript
bmesse.attachToPlusButton(
    function(){
	    //処理
    }
);
//Bメッセを表示
bmsClient.showCustomerFrameWithGroupKey(selector, groupKey, title);
```

引数に連想配列を渡してあげることで、最大４つまで機能をメニューに登録することができます。  

| メニュー非表示時 | メニュー表示時 |
|:------:|:------:|
| ![メニュー非表示時](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/attach_plus_button_close.JPG) |![メニュー表示時](https://raw.githubusercontent.com/flexfirm/bmesse-docs/img_branch/img/attach_plus_button_open.JPG)
  |

連想配列は以下の項目を定義する必要があります。  

| キー		  | 必須 	 | 説明 |
| :--			| :--:	 | :--- |
| `function`	|✔		| 実行したい処理の関数|
| `icon`		|✔		| アイコン用のSVGコード|
| `functionName`|		 | アイコンに表示する文字列|

```javascript
//割り当てたい機能を定義
var functionsAssociativeArray = [
    {
		//アイコンクリック時に実行させたい関数
		"function":function(){処理}
		//アイコン用svgコード
		"icon":'<svg height="100" width="100"><circle cx="50" cy="50" r="40" stroke="black" stroke-width="3" fill="red" /></svg>',
		//アイコンの下に表示する文字列
		"functionName":'テンプレートエディタ',
	},
	...//他に３つまで追加可能
];
//[+]に割り当てたい機能をセット
bmesse.attachToPlusButton(functionsAssociativeArray);
//Bメッセを表示
bmsClient.showCustomerFrameWithGroupKey(selector, groupKey, title);
```

<h2 id="その他設定">その他設定</h2>

### Shift-JIS対応
WebがShift_JISの場合、下記のファイルをShift_JISに変換して保存し利用してください。  
- c21-bms.js
- bmesse-config.js

<h2 id="用語">用語</h2>

#### Webアプリケーション
チャット機能を組み込む対象のWebアプリケーションのことを指します。

#### ユーザー
Bメッセを介してメッセージのやり取りを行うユーザーを指します。  
LINEやメールやWebチャットを利用するカスタマーのユーザーと、カスタマーへWebチャットを利用して返答を行うオペレーターのユーザーの2種類が存在します。

#### メッセージ
カスタマーとオペレーターの間で送受信されるテキストやファイルなどを指します。  
Bメッセでは、メッセージは全て吹き出しの形で表示されます。

#### チャット窓
ユーザー同士のメッセージの送受信と、表示ができる画面を指します。

#### スレッド
特定のユーザー同士の、チャット窓での一連のメッセージのやりとり、および一連のメッセージを表示している部分を指します。


<h2 id="変更履歴">変更履歴</h2>

* v 0.9.1 (2017/10/30)  
    * [BugFix] bmsClient.showFrameWithCustomerId() においてメッセージが表示されない場合がある不具合を修正しました。
    * [BugFix] タイムライン上のメッセージ表示順序がおかしくなる不具合を修正しました。 

<h2 id="ライブラリの利用">ライブラリの利用</h2>

### jQuery
Released under the license  
https://github.com/jquery/jquery/blob/master/LICENSE.txt  

### Google material-design-icons
Released under the license  
https://github.com/google/material-design-icons/blob/master/LICENSE

### spin.js
Released under the license  
https://github.com/fgnass/spin.js/blob/master/LICENSE.md

---
© [KSK Co., Ltd.](http://www.flexfirm.jp) All rights reserved.
