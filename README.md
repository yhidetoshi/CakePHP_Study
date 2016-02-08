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

