# Djangoで作成するマイクロブログ

[djangoドキュメント](https://docs.djangoproject.com/ja/2.2/intro/tutorial01/)

## 仮想環境の作成

Python を使って開発や実験を行うときは、用途に応じて専用の実行環境を作成し、切り替えて使用するのが一般的です。こういった、一時的に作成する実行環境を、「仮想環境」 と言います。

#### 仮想環境を使用する例
* システム全体で使うPython環境に影響を与えずにモジュールの追加・入れ替えをしたい。
* 同じモジュールの、複数のバージョンを使い分けたい。例えば、開発中のWebアプリケーション開発では、Webアプリケーションフレームワークとして Django の 1.10 を使い、新しいプロジェクトではバージョン 1.11 を使用したい場合など、簡単に切り替えられるようにしたい。
* 異なるバージョンの Python を使いたい。Python2 でしか利用できない、古いモジュールやアプリケーションを使用する場合、Python3.x と Python2.x を切り替えたい。

#### AnacondaでのPythonの仮想環境の作成
1. Anaconda Navigatorを開いて、ナビゲーターの左側にある「Environments」メニューを選択します。
2. 下の「Create」ボタンをクリックして、新規環境作成BOXが出てきますので、Name欄にわかりやすい環境の名前をつけてCreateボタンをクリックします。   
新規で仮想環境を作成したら、その新規環境が選択された状態になっています。右画面に表示されているものは導入されているパッケージが表示されています。

### ターミナルで仮想環境の実行
以降、仮想環境をターミナルから使用したい場合は次のようにします。
作成した仮想環境名の一覧表示をするコマンド
```python
conda env list
```
作成した仮想環境下に入るコマンド（MACの場合のコマンド、Winはsourceが不要）

```python
source activate 仮想環境名
```

現在の仮想環境から出る。（MACの場合のコマンド、Winはsourceが不要）
```python
source deactivate
```

## プロジェクトの作成

仮想環境に入る

```
source activate django_lesson
```

プロジェクト作成

```
 django-admin startproject microblog
```

以下のフォルダとファイルができる

```
.
├── manage.py
└── microblog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

manage.pyファイルを使ってアプリケーションを追加

```
./manage.py startapp blog
```
blogフォルダが作成されます。

```
.
├── blog
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
└── microblog
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-37.pyc
    │   └── settings.cpython-37.pyc
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

### settings.pyの編集

INSTALLED_APPSの内容に 'blog' を追加

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
]
```

言語を日本語に変更

```
LANGUAGE_CODE = 'ja'
```



### 仮想サーバー起動

```
./manage.py runserver
```

以下のように表示されたらアドレスをブラウザのURLに入れて表示させます。

Django version 2.2.1, using settings 'microblog.settings'

Starting development server at http://127.0.0.1:8000/

Quit the server with CONTROL-C.

サーバーの停止は表示の通りで、control + C

## 設計

Djangoでウェブアプリを設計するときに、データの保存を行うかどうかで道筋が変わります。

データを保存するとすれば、どのようなデータなのかを書き出すことで、設計が進みます。

今回のアプリはシンプルなマイクロブログです。

ブログとはいえ、twitterのように字数制限があるとします。これらをまとめると次のようになります。

* 投稿内容は文字列で140字以内
* 投稿日時は日付



### models.pyの編集

モデルはデータベースとのやりとりなどを受け持つことになります。

モデルの定義は blog/models.py にクラスを作成することで行います。

モデルのクラスは models.Modelクラスを継承します。



Blogクラスを作成します。

```
class Blog(models.Model):
    content = models.CharField(max_length=140, verbose_name='本文')
    posted_date = models.DateTimeField(auto_now_add=True)


```



models.CharFieldは文字列を格納するものです。

models.DateTimeFieldは、こちらも文字通り日付と時間を格納します。

Djangoのモデルフィールドは他にも複数存在します。必要に応じて調べてみましょう。

[モデルフィールドリファレンスの公式ドキュメント](https://docs.djangoproject.com/ja/2.2/ref/models/fields/)

idについては自動で定義されるようになっています。また、プライマリキーとして設定されています。



## データベースのマイグレーション

**データベースマイグレーション**とは アプリケーションで使う**データベース**の定義を自動的に作成・管理する機能です。

データベースの設定はsettings.pyに記述されています。

デフォルトではSQLightが使われています。

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

先ほどモデルの定義をクラスで作成しましたので、マイグレーションを行うことができます。

マイグレーション作成のコマンドは次の通りです。

```
./manage.py makemigrations
```

**Migrations for 'blog':**

  **blog/migrations/0001_initial.py**

​    \- Create model Blog

このように表示されればOKです。

blog フォルダの中の migrations フォルダ内に 0001_initial.py ファイルができました。

マイグレーションを行うたびにこのファイルのナンバーが変更されて出来上がります。

このファイル内にモデルで定義した内容が書き込まれています。idが自動で作成されているのも確認できます。

次にマイグレーションを実行する必要があります。コマンドは次の通りです。

```
 ./manage.py migrate
```

**Operations to perform:**

  **Apply all migrations:** admin, auth, blog, contenttypes, sessions

**Running migrations:**

  Applying contenttypes.0001_initial... **OK**

  Applying auth.0001_initial... **OK**

  Applying admin.0001_initial... **OK**

  Applying admin.0002_logentry_remove_auto_add... **OK**

  Applying admin.0003_logentry_add_action_flag_choices... **OK**

  Applying contenttypes.0002_remove_content_type_name... **OK**

  Applying auth.0002_alter_permission_name_max_length... **OK**

  Applying auth.0003_alter_user_email_max_length... **OK**

  Applying auth.0004_alter_user_username_opts... **OK**

  Applying auth.0005_alter_user_last_login_null... **OK**

  Applying auth.0006_require_contenttypes_0002... **OK**

  Applying auth.0007_alter_validators_add_error_messages... **OK**

  Applying auth.0008_alter_user_username_max_length... **OK**

  Applying auth.0009_alter_user_last_name_max_length... **OK**

  Applying auth.0010_alter_group_name_max_length... **OK**

  Applying auth.0011_update_proxy_permissions... **OK**

  Applying blog.0001_initial... **OK**

  Applying sessions.0001_initial... **OK**



このような表示が出ればOKです。

ここでサーバーを再度稼働させてみます。

```
./manage.py runserver
```

警告もなく稼働しました。



## ビューの作成

viewsはブラウザからのリクエストを受けるとアドレス(urls.py)の解決を行います。

その後viewsが呼び出されてmodls.pyからデータベースとのよりとりを行ないます。

view.py に返ってきたデータはテンプレートを通してレスポンスとしてユーザーのブラウザに表示されることになります。

views.pyの記述は関数で行う方法とクラスで行う方法の2つの方法があります。

### 関数でviewsを作成

関数を使う方法は比較的簡単な内容の場合です。

* viewsとは関数で記述されている
* それはURLにリクエストがあると呼び出される
* URLと関数はurls.pyによって紐付けられている

簡単な例

```
from django.http import HttpResponse
import datetime
def current_datetime(request):
    now = datetime.datetime.now()
    html = "<html><body>It is now %s.</body></html>" % now
    return HttpResponse(html)
```

Viewsの関数の特徴

* 関数は第一引数にHttpRequestというオブジェクトを受け取る
* HttpResponseを返さなければならない
* 第一引数はrequestとする

### クラスでviewsを作成

クラスベース汎用ビューでは、特定の処理を定義しているほかに、適切なデフォルト値を持っています。

オリジナルの処理を追加したい場合は適切なメソッドをオーバーライドすることでカスタマイズも容易です。

欠点としては、継承を多用しているため自身の処理を挿入するのにどのメソッドをオーバーライドすればよいのかわかりにくい。

#### ListViewの作成

ListViewは、クラスベース汎用ビューの一種で、viewsに関連づけられたモデルのリストを作成します。

```
from django.views.generic import ListView
from .models import Blog
 
 
class BlogListView(ListView):
    model = Blog
```

### urls.py作成

urls.pyはURLとviewsで定義した関数を結びつける役割をします。

Djangoでは、サイトにアクセスがあるとまずurls.pyで定義した設定にマッチするか照合されます。
そして、urls.pyで定義した設定にマッチすると、そこで結びつけられたviewを呼び出し、必要であれば引数としてパラメータを渡します。
viewはテンプレートを呼び出し、必要であればデータを渡します。
最後にそのテンプレートで作成されたデータをレスポンスとして返します。

```
from django.urls import path
from django.contrib import admin
from blog.views import BlogListView
urlpatterns = [
    path('', BlogListView.as_view(), name="index"),
    path('admin/', admin.site.urls),
]
```

### テンプレート作成

blogフォルダ内にtemplatesフォルダを作成する。

settings.py の TEMPLATES 部分の 'APP_DIRS': True, が True になっていることを確認します。

templates フォルダ内にさらに blog フォルダを作成して blog_list.html ファイルを作成する。

blog_list.html の名前は決まったものですから、　blog_listを表示するテンプレートはこの名前にします。

このファイルをテンプレートファイルとしてHTMLを記述する。



テンプレートで使う記号

* {% %}：プログラム的な命令を記述
* {{}}：値を表示する

**テンプレート作成の時{}はコピペを使わずにきちんキーボードから入力するようにしましょう。不具合が出ます**

今回表示されたのはBlogListViewから渡されたデータです。
重要なのは、リストの名前に当たるのが `object_list` です。これは決められた変数名です。
リストはfor文でループさせ、表示させます。

この記述にPythonのインデントは関係ありません。
そのため、ループの終わりを明示するために `endfor` を記述する必要があります。

```
{% for blog in object_list %}
	{{ blog.posted_date }}
    {{ blog.content }}
{% endfor %}
```

### adminページ作成

admin.py に次のコードを追加します。

```
from django.contrib import admin
from .models import Blog
 
admin.site.register(Blog) 
```

`127.0.0.1:8000/admin`のアドレスを入れるとDjango administrationのページが開きます。

確認できたら、一旦サーバーを停止します。

ユーザー登録は次のように行います。ユーザー名を指定しなければ現在のユーザー名になります。

```
./manage.py createsuperuser
Username (leave blank to use 'tahara'): 
Email address: 
Password: 
Password (again): 
Superuser created successfully.

```

再びサーバーを稼働します。

作成したユーザー名とパスワードを入力します。

下の方のblogを選ぶと右上のADD BLOG＋をクリックします。

ブラウザのアドレスを `127.0.0.1:8000`とすると入力した内容を確認することができます。

## 個別ページを作成（DetailView）

個別のページを作成するのは非常に簡単です。
個別のページを表示するためのクラスベース汎用ビューはDetailViewというクラスです。そのため、個別ページのことをDetailViewと呼びます。



次のコードを views.py に追加します。

```
from django.views.generic import DetailView
from .models import Blog

class BlogDetailView(DetailView):
    model = Blog
```



リスト表示と合わせると次のようになります。

```
from django.views.generic import DetailView
from django.views.generic import ListView
from .models import Blog
 
 
class BlogListView(ListView):
    model = Blog

class BlogDetailView(DetailView):
    model = Blog
```



#### URLの設定

個別ページのアドレスは少し工夫が必要です。

まずはコードを記述します。

urls.py の`urlpatterns`に `path('<int:pk>',BlogDetailView.as_view(),name='detail')`を追加します。

```
urlpatterns = [
	# path('<URL>',views(関数), ニックネーム)
    path('admin/', admin.site.urls),
    path('',BlogListView.as_view(),name='index'),
    path('<int:pk>',BlogDetailView.as_view(),name='detail'),
]
```

上記のコードで気になるのは、「<int:pk>」部分です。intは何らかの整数を表して、pkは変数的な役割をするもので、何らかの整数がpkに代入されることを意味します。

尚、pkはプライマリキーのことで通常idがpkとなります。

つまりこのpath関数で 

/1
/10
/100

などのURLアドレスの末尾にマッチします。

### DetailViewのテンプレート

DetailViewのテンプレートは「blog_detail.html」という名前で、`blog/templates/blog`  フォルダの中に作成します。

基本コードは次のようになります。

```
<!DOCTYPE html>
<html lang="ja">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>
<h1>Blog</h1>
<div>
	
</div>	
</body>
</html>
```



内容を表示するには、divタグの中に次の記述を入れます。

objectというのは、データが入っている変数です。これはデフォルト値でBlogモデルを表しています。

Blogモデルにはcontentとposted_dateを保存していますので、contentを表示するには次のように書きます。

```
<h2>{{ object.content }}</h2>
```

日付のposted_dateを表示するには次のようにします。

```
<p>{{ object.posted_date}}</p>
```



早速サーバーを稼働させて確認します。

URLアドレスは `http://127.0.0.1:8000/1` または1を2か3に変更することでadminで作成した3つの記事が確認できます。

### テンプレートの継承

HTMLの共通部分を一つにすると使いやすくなります。

baseになるHTMLを作成します。ファイル名は自由ですがbase.htmlとします。

注意するべき点は、templateフォルダの直下に作成することです。

```
<!DOCTYPE html>
<html lang="ja">
<head>
	<meta charset="UTF-8">
	<title>Blog</title>
</head>
<body>
<h1>Blog</h1>
<div>
	{% block body %}{% endblock %}
</div>	
</body>
</html>
```

そしてblog_list.htmlを次のように修正します。

```
{% extends "base.html" %}
{% block body %}
  {% for obj in object_list %}
  <div>
    {{ obj.content }}<br>
    {{ obj.posted_date }}
  </div>
  {% endfor %}
{% endblock %}
```

さらにblog_detail.htmlを次のように修正します。

```
{% extends "base.html" %}
{% block body %}
<h2>{{object.content}}</h2>
<p>{{object.posted_date|date:"Y/m/d h:m:s"}}</p>
{% endblock %}
```

これでHTMLの共通部分のテンプレート継承ができました。



## CSSを使ってデザイン

[Clean Blog](http://startbootstrap.com/template-overviews/clean-blog/) の無料テンプレートを使います。

ダウンロードしたファイルからindex.htmlを全てコピーしてから、base.htmlに貼り付けます。

その後、一番先頭行に次の内容を記述します。

```
{% load static %}
```

これは、CSSファイルや画像ファイルなど保存するフォルダと関連させるものです。

CSSのlink設定を次のように変更します。

```
<link href="{% static 'vendor/bootstrap/css/bootstrap.min.css' %}" rel="stylesheet">
```

javascriptの読み込みも同様に行います。

```
<script src="{% static 'vendor/jquery/jquery.min.js' %}"></script>
```

続いてsetting.pyのSTATIC_URLのしたに以下を追加します。

```
STATICFILES_DIRS =(
    os.path.normpath(os.path.join(BASE_DIR,"assets")),)
```





次にmanage.pyと同階層にassetsフォルダを作成します。

microblog/assetsとなるようにします。



そして、assetsフォルダに [Clean Blog](http://startbootstrap.com/template-overviews/clean-blog/) からダウンロードしてきたCSSフォルダ、imgフォルダ、jsフォルダ、venderフォルダをフォルダごと入れます。



BASE_DIRはプロジェクトフォルダのパスを表します。

つまりルートパスを取得します。



base.htmlのbody部分については、`<div class="container"></div>` で囲まれた部分を全て削除します。

そして、その代わりに以下の内容にします。

```
    <!-- Main Content -->
    <div class="container">
      {% block body %}{% endblock %}  
    </div>
```

つまり`<div class="container"></div>` に各テンプレートを読み込むということです。



```
    <!-- Main Content -->
    <div class="container">
      {% block body %}{% endblock %}  
    </div>
```

次に、CSSやJavaScript、画像のリンク設定をDjango仕様に変更します。

CSSのリンクはサイト外のリンク指定（CDN指定など）以外は全て以下の様にします。

```
<link href="{% static 'vendor/bootstrap/css/bootstrap.min.css' %}" rel="stylesheet">
```

JavaScriptや画像の読み込みも同様です。

```
<script src="{% static 'vendor/jquery/jquery.min.js' %}"></script>
```

```
style="background-image: url({% static 'img/home-bg.jpg' %})"
```



blog_list.htmlの編集

テンプレートのCSSのデザインをうまく生かす様にCSSクラス名をそのまま活用します。

```
{% extends "base.html" %}
{% block body %}
<div class="row">
            <div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">
            	{% for obj in object_list %}
                <div class="post-preview">
                    <a href="post.html">
                        <h2 class="post-title">
                           {{ obj.content }}
                        </h2>
                    </a>
                    <p class="post-meta">
						 {{ obj.posted_date }}
                    </p>
                </div>
                {% endfor %}
 </div> 
{% endblock %}
```





dlog_detail.htmlの編集

テンプレートのCSSのデザインをうまく生かす様にCSSクラス名をそのまま活用します。

```
{% extends "base.html" %}
{% block body %}
<div class="row">
            <div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">
                <div class="post-preview">
                    <a href="post.html">
                        <h2 class="post-title">
                           {{object.content}}
                        </h2>
                    </a>
                    <p class="post-meta">
						 {{object.posted_date|date:"Y/m/d h:m:s"}}
                    </p>
                </div>
 </div> 
{% endblock %}

```



## ナビゲーション

ナビゲーションはbase.htmlにそのまま書いても良いのですが、

汎用性を持たせるためにも、別ファイルとします。

base.htmlと同じ階層にナビゲーション用の新規テンプレートを作成します。

ナビゲーションファイルを別ファイルにします。

ナビゲーションファイルは base.html ファイルと同じ階層に作成します。

ファイル名はnavi.htmlとします。

navi.html の内容はbase.htmlよりナビゲーション部分を切り取って貼り付けます。

今の所特に仕掛けはありません。

navi.htmlのコード

```
    <!-- Navigation -->
    <nav class="navbar navbar-default navbar-custom navbar-fixed-top">
        <div class="container-fluid">
            <!-- Brand and toggle get grouped for better mobile display -->
            <div class="navbar-header page-scroll">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
                    <span class="sr-only">Toggle navigation</span>
                    Menu <i class="fa fa-bars"></i>
                </button>
                <a class="navbar-brand" href="index.html">Start Bootstrap</a>
            </div>

            <!-- Collect the nav links, forms, and other content for toggling -->
            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                <ul class="nav navbar-nav navbar-right">
                    <li>
                        <a href="index.html">Home</a>
                    </li>
                    <li>
                        <a href="about.html">About</a>
                    </li>
                    <li>
                        <a href="post.html">Sample Post</a>
                    </li>
                    <li>
                        <a href="contact.html">Contact</a>
                    </li>
                </ul>
            </div>
            <!-- /.navbar-collapse -->
        </div>
        <!-- /.container -->
    </nav>
```

base.html で navi.html の内容を読み込むには、読み込みたい場所に次のコードを入れます。

```
{% include "navi.html" %}
```

単純に他のテンプレートを読み込む場合はinclude文を使うと簡単に行えます。



テンプレートの探索については別途しっかりとマスターする様にしましょう。



## CRUD作成

### Createの作成

createはクラスベース汎用ビューの一つであるCreateViewを使うことで簡単に仕上げることができます。

今まで出てきたクラスベース汎用ビューにはListView、DetailViewがあります。これは閲覧するための汎用ビューでした。

CreateViewは、ウェブアプリケーションのデータを入力するビューで使われます。

class BlogCreateViewを作成する際に、なんでも入力できない様にfieldsの設定があります。

セキュリティ上の効果もありますので必ず設定します。

views.py に追記

```
from django.views.general import CreateView

class BlogCreateView(CreateView):
	model = Blog
	fields = ["contenr","posted_date"]
```

### urlの設定

success_urlですが、新たなデータが保存された後、どこかのページに移動します。
これを、リダイレクトと言ったりもしますが、どのページに移動するのかを設定します。

今回はreverse_lazyというヘルパー関数を使用しています。
indexというのはurls.pyでURLにつけたニックネームです。
つまり、BlogListViewで表示するページに移動します。トップページです。

urls.pyの設定

```
from django.urls import path
from django.contrib import admin
from blog.views import BlogListView
from blog.views import BlogDetailView
from blog.views import BlogCreateView
 
urlpatterns = [
    path('create', BlogCreateView.as_view(), name="create"),
    path('detail/<int:pk>', BlogDetailView.as_view(), name="detail"),
    path('', BlogListView.as_view(), name="index"),
    path('admin/', admin.site.urls),
]

```





### 入力フォーム作成

Djangoの入力フォームテンプレートは以下の様に記述します。

ファイルは templates/blog/blog_form.html として作成します。

```
{% extends "base.html" %}
 
<form method="POST">{% csrf_token %}
<table class="table">
{‌{ form }}
</table>
<button type="submit">送信</button>
</form>
```

{% csrf_token %}はCSFR攻撃の対策つまりTokenを発行するものです。

この記述がないとフォームはうまく動作しない様になっています。

## Update

UPdateは結構難しいです。とりあえずはチュートリアルにしたがって動く様にして、後ほどその仕組みを考察すると良いと思います。

UpdateViewを使ってアップデートする仕組みを作ります。

view.pyを次の様にします。

```
from django.views.generic import UpdateView
 
class BlogUpdateView(UpdateView):
     model = Blog
     fields = ["content", ]
     def get_success_url(self):
         return reverse_lazy("detail", kwargs={"pk": self.kwargs["pk"]})
```

get_success_urlの使い方がポイントになります。

いままでは、success_urlという変数を定義してました。
静的なURLであれば success_urlで良いのですが、動的に変化するURLでは別のやり方になります。

今回のページの動作は投稿を更新したら、その後更新した記事のBlogDetailViewで表示されるページに移動したいと考えています。
この様な状態の時、URLがそのときによって違う＝動的なURLということになります。

このような場合は関数get_success_urlを定義して、URLを返すようにします。
reverse_lazyに取り出したIDを設定してあげればいいわけです。



アップデートのテンプレートはcreateのテンプレートと同じもの blog_form.html を使います。

urls.pyを編集

```
from django.urls import path
from django.contrib import admin
from blog.views import BlogListView, BlogDetailView, BlogCreateView
from blog.views import BlogUpdateView
 
urlpatterns = [
    path('create', BlogCreateView.as_view(), name="create"),
    path('update/<int:pk>', BlogUpdateView.as_view(), name="update"),  # this line
    path('detail/<int:pk>', BlogDetailView.as_view(), name="detail"),
    path('', BlogListView.as_view(), name="index"),
    path('admin/', admin.site.urls),
]
```

## Formの作成

blogフォルダにform.pyを新規に作成します。

Djangoではフォームを簡単に扱えるようにフォームクラスが用意されています。
基本的にはモデルに紐付くModelFormクラスを使用します。
検索フォームのようにモデルに紐付かないものはFormクラスを使用します。

フォームを定義するためのファイルとしてblogフォルダの中にforms.pyというファイルを作成してください。
フォームを定義するファイル名としてforms.pyを使用することは慣例となっています。

\# forms.py

```
from django import forms
from .models import Blog
 
class BlogForm(forms.ModelForm):
     class Meta:
         model = Blog
         fields = ["content", ]
```

さて、次のこのフォームをBlogCreateViewとBlogUpdateViewに適用していきます。

 views.py

```
from .forms import BlogForm
 
class BlogCreateView(CreateView):
     form_class = BlogForm
 
class BlogUpdateView(UpdateView):
     form_class = BlogForm
```

これにより、テンプレートのファイル名が変更になります。
テンプレートのファイル名はblog_form.htmlとなります。

## Delete

削除機能はDeleteViewクラスベース汎用ビューで提供されています。


views.pyに追加します。

```
from django.views.generic import CreateView, UpdateView, DeleteView

class BlogDeleteView(DeleteView):
	model = Blog
	success_url = reverse_lazy("index")
```

削除された後は、トップページにリダイレクトされます。

次は、テンプレートの名前は、blog_confirm_delete.htmlです。
役割は、名前の通り削除の確認です。

\# blog_confirm_delete.html

```
{% extends "base.html" %}
 
{% block body %}
 <h1>削除の確認</h1>
 <p>本当に削除してもよろしいですか？</p>
 <form method="post">
 {% csrf_token %}
 {‌{form}}
 <button type="submit" class="btn btn-danger">削除</button>
 <a href="{% url 'index' %}" class="btn btn-default">キャンセル</a>
{% endblock %}
```

urls.pyを編集します。

```
urlpattern = [
     ...
     path('delete/<int:pk>', BlogDeleteView.as_view(), name="delete"),
]
```

## 各ページリンク設定

urls.py

```
from django.contrib import admin
from django.urls import path
from blog.views import BlogListView, BlogDetailView, BlogCreateView,BlogUpdateView,BlogDeleteView

urlpatterns = [
	# path('<URL>',views(関数), ニックネーム)
    path('admin/', admin.site.urls),
    path('',BlogListView.as_view(),name='index'),
    path('<int:pk>', BlogDetailView.as_view(), name='detail'),
    path('<int:pk>/update', BlogUpdateView.as_view(), name='update'),
    path('<int:pk>/delete', BlogDeleteView.as_view(), name='delete'),
    path('create', BlogCreateView.as_view(), name='create'),
]
```

blog_list.html

```
{% extends "base.html" %}
{% block body %}
<div class="row">
            <div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">
            	{% for obj in object_list %}
                <div class="post-preview">
                    <a href="{% url 'detail' obj.id %}">
                        <h2 class="post-title">
                           {{ obj.content }}
                        </h2>
                    </a>
                    <p class="post-meta">
						 {{ obj.posted_date }}
                    </p>
                </div>
                {% endfor %}
 </div> 
{% endblock %}
```

blog-detail.html

```
{% extends "base.html" %}
{% block body %}
<div class="row">
            <div class="col-lg-8 col-lg-offset-2 col-md-10 col-md-offset-1">
                <div class="post-preview">                   
                        <h2 class="post-title">
                           {{object.content}}
                        </h2>
                    <p class="post-meta">
						 {{object.posted_date|date:"Y/m/d h:m:s"}}
                    </p>
                </div>
                <a href="{% url 'update' object.id %}" class="btn btn-primary">編集</a>
                <a href="{% url 'delete' object.id %}" class="btn btn-primary">削除</a>
 </div> 
{% endblock %}
```

ナビゲーションのリンクはhome部分だけにしていかに変更

```
<a class="navbar-brand" href="{% url 'index' %}">Blog</a>
```

```
<ul class="nav navbar-nav navbar-right">
    <li>
        <a href="{% url 'index' %}">Blog</a>
    </li>
                    
</ul>
```

### 新規作成ページと編集ページのテンプレート作成

それぞれのテンプレートを個別に作成するには個別のテンプレートを命名規則に乗っとて作成します。

そして、view.pyの記述に関連づけをすることで実現できます。

blog_create_form.html作成

```
{% extends 'base.html' %}

{% block body %}
<h1>新規作成</h1>
<form method="post">
	{% csrf_token %}
	<table class="table">
	{{ form }}
	</table>
	<button class="btn btn-primary" type="submit">送信</button>
</form>
{% endblock %}
```

blog_update_form.html作成

```
{% extends 'base.html' %}

{% block body %}
<h1>編集</h1>
<form method="post">
	{% csrf_token %}
	<table class="table">
	{{ form }}
	</table>
	<button class="btn btn-primary" type="submit">送信</button>
</form>
{% endblock %}
```

view.jsの変更 

class変数template_nameを追加してファイル名をパス付きで指定します。

```
class BlogCreateView(CreateView):
	model = Blog
	form_class = BlogForm
	success_url = reverse_lazy("index")
	template_name = "blog/blog_create_form.html"

class  BlogUpdateView(UpdateView):
	model = Blog
	form_class = BlogForm
	template_name = "blog/blog_update_form.html"
	def get_success_url(self):
		blog_pk = self.kwargs['pk']
		url = reverse_lazy("detail",kwargs={"pk":blog_pk})
		return url

```

### メッセージの表示

投稿を新しく作成したり、更新したり、削除したり、そういったことが正常に終了したのか、エラーが出たのかを通知するメッセージを作成します。

メッセージ機能はdjangoのメッセージフレームワークで用意されています。

 views.py編集

```
from django.contrib import messages 

class BlogCreateView(CreateView):
     def form_valid(self, form):
         messages.success(self.request, "保存しました")
         return super().form_valid(form)

```

messges.successは成功したときのメッセージを登録するという意味です。
引数のself.request, "保存しました" は「保存しました」と表示させるものです。

次はテンプレートの記述です。

base.html に次の様に追記します。

```
 <div class="container">
        {% if messages %}
        <ul class="list-unstyled">
        {% for message in messages %}
        <li{% if message.tags %} class="bg-{% if message.tags == 'error' %}danger{% else %}{{ message.tags }}{% endif %} alert"{% endif %}>
        {{ message }}
        <button type="button" class="close" aria-label="Close" data-dismiss="alert"><span aria-hidden>&times;</span></button>
        </li>
        {% endfor %}
        </ul>
        {% endif %}
      {% block body %}{% endblock %}  
    </div>
```

アップデートのメッセージ

views.py

```
class  BlogUpdateView(UpdateView):
    model = Blog
    form_class = BlogForm
    template_name = "blog/blog_update_form.html"
    def get_success_url(self):
        blog_pk = self.kwargs['pk']
        url = reverse_lazy("detail",kwargs={"pk":blog_pk})
        return url
    def form_valid(self,form):
        messages.success(self.request,"更新されました")
        return super().form_valid(form)
    def form_invalid(self,form):
        messages.error(self.request,"更新できませんでした")
        return super().form_invalid(form)

```



削除のメッセージ

views.py

```
class BlogDeleteView(DeleteView):
    model = Blog
    success_url = reverse_lazy("index")
    def delete(self,request, *args, **kwargs):
        messages.success(self.request,"削除しました")
        return super().delete(request,*args,**kwargs)
 
```

## ログイン機能

1.ユーザーを作成（すでにスーパーユーザーを作成してあるので省略）
2.作成、更新、削除ページはログインしないと表示できないようにする
3.一覧、個別ページにはログインしているときのみ表示される編集などのボタンを作成
4.ログイン画面を作る

views

```
from django.contrib.auth.mixins import LoginRequiredMixin
 
class BlogCreateView(LoginRequiredMixin, CreateView):
    login_url = '/login/'
     ...
 
class BlogUpdateView(LoginRequiredMixin, UpdateView):
     login_url = '/login/'
     ...
 
class BLogDeleteView(LoginRequiredMixin, DeleteView):
     login_url = '/login/'
     ...

```

blog_list.html

```
{% if user.is_authenticated %}
<p><a class="btn btn-primary" href="{% url "create" %}">NEW</a></p>
{% endif %}
```

blog_detail.html

```
{% if user.is_authenticated %}
<a href="{% url "update" object.id %}" class="btn btn-default">編集</a>
<a href="{% url "delete" object.id %}" class="btn btn-danger">削除</a>
{% endif %}
```

navi.html

```
 <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a href="{% url 'index' %}">Home</a>
            </li>
            {% if user.is_authenticated %}
                {# ログインしているとき #}
                <li><a href="#">{‌{ user.username }}</a></li>
                <li><a href="{% url 'logout' %}">ログアウト</a></li>
            {% else %}
                {# ログインしていない時#}
                {% if "logout" in request.path %}
                    <li><a href="{% url 'login' %}">ログイン</a></li>
                {% else %}
                    <li><a href="{% url 'login' %}?next={‌{ request.path }}">ログイン</a></li>
                {% endif %}
            {% endif %}
        </ul>
    </div>
<!-- /.navbar-collapse -->
```

これで、ログインした時にしか表示されないボタンが追加されました。

最後に、ログイン画面を作ります。
また、ログインするということは、ログアウトさせることも必要ですのでログアウトも追加します。

blog/templates/login.html

```
{% extends "base.html" %}

{% block body %}
    <form method="post" action="{% url 'login' %}">
        {% csrf_token %}
        <table class="table">
            {{ form }}
        </table>
        <input type="hidden" name="next" value="{‌{ request.GET.next }}"/>
        ログイン
    </form>
{% endblock %}
```




blog/templates/logout.html

```
{% extends "base.html" %}
 
{% block body %}
<h1>ログアウトしました</h1>
{% endblock %}
```



urls.py

```
from django.contrib.auth.views import LoginView, LogoutView

urlpatterns = [
 ...
    path('login/', LoginView.as_view('template_name': "login.html"), name="login"),
    path('logout/', LogoutView.as_view('template_name': 'logout.html'), name="logout"),
]
```

