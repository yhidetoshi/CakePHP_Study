# dev_cakephp

- デフォルト設置場所 & 共通設置場所
```    
(Path):/var/www/cakephp/app/webroot/css/cake.generic.css
```

- CSSの反映
```    
(Path):/var/www/cakephp/app/View/Layouts/default.ctp
echo $this->Html->css('cssファイル名');
※ .cssの拡張子は書かない
```

- デバッグレベルの設定
```    
(Path):/var/www/cakephp/app/Config/core.php
- Configure::write('debug', 2);
 → default:2

0: プロダクションモード。エラーは出力されず、デバッグメッセージも表示しない。
1: 開発モード。 Warnings とエラー、デバッグメッセージを表示される。
2: 開発モード。実行されたSQL 文が表示される。
3: 開発モード。2に加えて、現在のオブジェクト（通常はコントローラ）のフルダンプも表示される。
```

- Modelクラス(User.php)
```    
(Path):/var/www/cakephp/app/Model/Hoge.php
※ ファイル名:大文字で始まり、単数形で作成(英語の複数形に準ずるので注意)
・ルールなどを作成
　→ バリデーション
```

- バリデーションの例(User.phpの一部)
```    
public $validate = array(
	'email' => array(
		'required' => array(
			'rule' => 'notBlank',
			'message' => 'メールアドレスは必要です',
			'rule' => 'isUnique',
			'message' => '同じメールアドレスがすでに使用されています'
		)
	),
	'password' => array(
		'required' => array(
			'rule' => 'notBlank',
			'message' => 'A password is requred',
			'rule' => array('minLength', 5),
			'message' => '５文字以上にしてください'
		)
	),		
);

```

- Viewクラス(User)
```    
(Path):/var/www/cakephp/app/View/Users/
[add.ctp  edit.ctp  index.ctp  login.ctp  mypage.ctp]
```

- Viewクラスのファイル命名
```    
(サイトURL): http://cakep.hidetoshi.xyz/users/mypage
[add.ctp  edit.ctp  index.ctp  login.ctp  mypage.ctp]
```

- Controllerクラス(User)
```    
(Path):/var/www/cakephp/app/Controller/
UsersController.php
※ 単語は複数形, Controllerをつける
class UsersController extends AppController{
 → AppControllerクラスを継承する
```

- ログイン認証
```    
Userモデルに[username],[password]という項目があった場合、下記の通りに記述する(View)login.ctp
echo $this->Form->create();
echo $this->Form->input('username');   
echo $this->Form->input('password');   
echo $this->Form->end('Login');
```

- コンポーネント($components)
```    
public $components = array
(
	'Flash','Session',
	'Auth' => array
	(
		'loginRedirect' => array
		(
			'controller' => 'users',
			'action' =>'index'				
		),
		'logoutRedirect' => array
		(
			'controller' => 'users',
			'action' => 'login'
		),
		'authenticate' => array
		(
			'Form' => array
			(
				'passwordHasher' => 'Blowfish'
			)
		)
	)
);
・loginRedirect  : ログイン後の処理を記述
・logoutRedirect : ログアウト後の処理を記述
・authenticate   : 認証で使うパスワードの暗号化設定 

(例)
---
'controller' => 'users' (Userクラス)
'action' => 'index' (index.ctp: /index に遷移する)
---
```

- beforeFilter( )
```    
public function beforeFilter()
{
	$this->Auth->allow('login','add');
}
 ・ Authの認証が通る前、つまりログインしていない場合でアクセスできるページを記述
　→ この場合、/login /add (login.ctp, add.ctp)が閲覧できる

```

- Linkの作成 ( View hoge.ctp )
```    
echo $this->Html->link('logout', array('controller' => 'users', 'action' => 'logout'));
 → $this->Html->link('リンク名', array('controller' => 'コントーラ名', 'action' => '表示させるURL /名前(.ctpの名前)'))	
```

- Sessionの設定
```    
※ ドット記法 により配列構造で作成可能
@Controllerのcomponentsに追加
public $components = array(
		'Flash','Session',
);

//セッションの生成
$this->Session->write('User.username', $this->Auth->user('username'));

//セッションからデータ取得
$session_id = $this->Session->read('User.username');

//セッションの解放
$this->Session->delete("User.username");
```

- Passwordのハッシュ化
```    
public function beforeSave($options = array())
{
	if(isset($this->data[$this->alias]['password']))
	{
		$passwordHasher = new BlowfishPasswordHasher();
		$this->data[$this->alias]['password'] = $passwordHasher->hash(
			$this->data[$this->alias]['password']
		);
	}
		return true;
}
```

- プルダウンメニュー(View hoge.ctp)
```    
echo $this->Form->input('sex',
	array('label' => '性別', 'options' => 
	array('男性' =>'男性','女性' =>'女性')
	)
	);
```

- Javascriptを表示させる方法1(View hoge.ctp)
```    
<?php echo $this->Html->scriptStart(array('inline' => false)); ?>
var firstname;
firstname = "Hoge";
document.write(firstname);

firstname="Huga";
document.write(firstname);

<?php $this->Html->scriptEnd()?>
```

- Helperのラッピング場所
```    
ls /var/www/cakephp/lib/Cake/View/Helper/
CacheHelper.php  HtmlHelper.php          JsHelper.php              PaginatorHelper.php        SessionHelper.php
FlashHelper.php  JqueryEngineHelper.php  MootoolsEngineHelper.php  PrototypeEngineHelper.php  TextHelper.php
FormHelper.php   JsBaseEngineHelper.php  NumberHelper.php          RssHelper.php              TimeHelper.ph

Viewで使ってる$this->Form->inputなどはここでラッピングされている
```

- ラッピング場所2
```    
(例) $this->set('key_username', $session_id);
/var/www/cakephp/lib/Cake/View/View.php

 814         public function set($one, $two = null) {
 815                 $data = null;
 816                 if (is_array($one)) {
 817                         if (is_array($two)) {
 818                                 $data = array_combine($one, $two);
 819                         } else {
 820                                 $data = $one;
 821                         }
 822                 } else {
 823                         $data = array($one => $two);
 824                 }
 825                 if (!$data) {
 826                         return false;
 827                 }
 828                 $this->viewVars = $data + $this->viewVars;
 829         }
```
