{{# Specify extended template #}
{% extends "./leanengine_webhosting_guide.tmpl" %}

{% set productName = 'LeanEngine' %}
{% set platformName = 'PHP' %}
{% set fullName = productName + ' ' + platformName %}
{% set sdk_name = 'PHP' %}
{% set leanengine_middleware = '[LeanCloud PHP SDK](https://github.com/leancloud/php-sdk)' %}

{% block custom_api_random_string %}
{{productName}} 允许开发者自定义基于 HTTP（HTTPS） 的 API。Allowing you to custom API based on HTTP（HTTPS).
例如，开发者如果想实现一个获取服务端时间的 API，可以在代码中如下做：
For example, if you want to implement an API that can obtain the server time, you can write the following code:


打开 `./app.php` ，添加如下代码：
Add the following code into the file `./app.php`:

```php
$app->get('/time', function($req, $res) {
    // PSR-7 response is immutable
    $response = $res->withHeader("Content-Type", "application/json");
    $response->getBody()->write(json_encode(array(
        "currentTime" => date(DATE_ATOM)
    )));
    return $response;
});
```

然后打开浏览器，访问 <http://localhost:3000/time>，浏览器应该会返回如下类似的内容：
Then open the browser, visit <http://localhost:3000/time>, the browser should return the following content:
```json
{"currentTime":"2016-02-01T09:43:26.223Z"}
```

部署到云端后，你可以通过 `http://{{var_app_domain}}.leanapp.cn/time` 来访问该 API。你的 iOS 或者 Android 的程序就可以构建一个 HTTP 请求获取服务端时间了。当然还是建议使用各 SDK 内置的获取服务器时间的 API，这里的例子只是演示。
After you deploy to the cloud, you can access the API via `http://{{var_app_domain}}.leanapp.cn/time`. If you have an iOS or Android program, you should be able to construct a HTTP to request the server time.Of course, we still recommend you use the built-in API in SDK to obtain the server time, the example here is just a tutorial.
{% endblock %}

{% block getting_started %}

将示例代码 [slim-getting-started](https://github.com/leancloud/slim-getting-started) 克隆到本地：
Clone the example code [slim-getting-started](https://github.com/leancloud/slim-getting-started)to local:

```sh
git clone https://github.com/leancloud/slim-getting-started.git
```

使用 composer 安装第三方依赖：
Using composer to install the third-party dependency:

```sh
composer install
```
{% endblock %}

{% block project_constraint %}

## 项目骨架 Project skeleton

你的项目需要遵循一定格式才会被云引擎识别并运行。
Your project can only be recognized by LeanEngine and operate normally by following a certain format.

{{fullName}} 项目必须有 `$PROJECT_DIR/public/index.php` 文件，该文件为整个项目的启动文件。
{{fullName}} project must contain `$PROJECT_DIR/public/index.php`, this file is the startup file for the whole project.
{% endblock %}



{% block ping %}
{{leanengine_middleware}} 内置了该 URL 的处理，只需要将中间件添加到请求的处理链路中即可：Builtin processing for this URL, you only need to add the middleware to the requested processing link.

```
$engine = new SlimEngine();
$app->add($engine);
```

如果未使用 {{leanengine_middleware}}，则需要自己实现该 URL 的处理，比如这样：
If you didn't use {{leanengine_middleware}}, you need to implement the processing of the URL by yourself like this:
```
// 健康监测 router  Router health monitoring
$app->get('/__engine/1/ping', function($req, $res) {
    // PSR-7 response is immutable
    $response = $res->withHeader("Content-Type", "application/json");
    $response->getBody()->write(json_encode(array(
        "runtime" => "php-" . phpversion(),
        "version" => "custom"
    )));
    return $response;
});

// 云函数列表 Cloud functions list
app.get('/1.1/_ops/functions/metadatas', function(req, res) {
    $response = $res->withHeader("Content-Type", "application/json");
    $response->getBody()->write(json_encode(array(
        "result" => array()
    )));
    return $response;
});
```
{% endblock %}

{% block supported_frameworks %}

{{fullName}} 不依赖第三方框架，你可以使用你最熟悉的框架进行开发，或者
{{fullName}} does not depend on any third-party frameworks, you can develop it by using the framework you are familiar with, or:
不使用任何框架。但是请保证通过执行 `public/index.php` 能够启动你的项目。
not using any framworks, but please make sure you can start your project by executing `public/index.php`

对于 PHP 项目，我们默认每 64M 内存分配一个 PHP-FPM Worker，如果希望自定义 Worker 数量，可以在云引擎设置页面的「自定义环境变量」中添加名为 PHP_WORKERS 的环境变量，值是一个数字。设置过低会导致收到新请求时无可用的 Worker；过高会导致内存不足、请求处理失败，建议谨慎调整。
For PHP project, by default, we assign a PHP-FPM Worker for every 64M RAM. If you want to custom the amount of Worker, you can add an environment variable named PHP_WORKERS to the 「custom environment variable」on the setup page of the LeanEngine, the value of this environment variable is a number. If you set the value too low, you will not have available Worker when you receive a new request; if you set the value too high, you will not have enough RAM to have a successful request. Hence, please adjust the value cautiously. 
{% endblock %}

{% block code_get_client_ip_address %}
```php
$app->get('/', function($req, $res) {
  error_log($_SERVER['HTTP_X_REAL_IP]); // 打印用户 IP 地址 Print user IP address
  return $res;
});
```
{% endblock %}

{% block use_leanstorage %}

## 使用数据存储服务 Using Data stroage service

云引擎使用 {{leanengine_middleware}} ，实际包含了存储 SDK，可以直接使用相关接口来存储数据。请参考 [PHP 存储文档](leanstorage_guide-php.html)。
LeanEngine uses  {{leanengine_middleware}}, which contains the storage SDK; you can use the relevant port to store your data. Please refer to [PHP Storage file](leanstorage_guide-php.html).

如果使用项目框架作为基础开发，{{leanengine_middleware}} 默认提供了支持 [Slim 框架](http://www.slimframework.com)的中间件，可以根据示例程序的方式直接使用。
If you use the project framework to do basic development, {{leanengine_middleware}} provides the middleware that supports the [Slim framework]. You can use it directly according to the example program.


如果是自定义项目，则需要自己配置： If you develope a custom project, you need to configure as follows:

* 首先安装 First install [composer](https://getcomposer.org)

* 配置依赖：在项目根目录下执行以下命令来增加 {{leanengine_middleware}} 的依赖：Configure dependency: Execute the following command under the project root directory to increase the dependency of the {{leanengine_middleware}}:

```
composer require leancloud/leancloud-sdk
```

* 初始化：在正式使用数据存储之前，你需要使用自己的应用 key 进行初始化中间件：Initialization: Before you store your date officially, you need to initialize the middleware by using your application key:

```php
use \LeanCloud\Client;

Client::initialize(
    getenv("LC_APP_ID"),          // 从 LC_APP_ID 这个环境变量中获取应用 app id 的值 Obtain the value of the app id from this environment variable LC_APP_ID.
    getenv("LC_APP_KEY"),         // 从 LC_APP_KEY 这个环境变量中获取应用 app key 的值 Obtain the value of the app key from this environment variable LC_APP_KEY.

    getenv("LC_APP_MASTER_KEY")   // 从 LC_APP_MASTER_KEY 这个环境变量中获取应用 master key 的值 Obtain the value of the master key from this environment variable LC_APP_MASTER_KEY.
);

// 如果不希望使用 masterKey 权限，可以将下面一行删除 If you don't want to use the permission of the masterkey, you can delete the following line.

Client::useMasterKey(true);
```
{% endblock %}

{% block http_client %}

云引擎 PHP 环境可以使用内置的 curl 模块，不过我们推荐使用 guzzle 等第
三方库来处理 HTTP 请求。
LeanEngine PHP environment supports the built-in curl module, however, we recommend to use a third party library like guzzle to process the HTTP request.


安装 guzzle:

```sh
composer require guzzlehttp/guzzle:~6.0
```

代码示例： Example code:

```php
$client = new GuzzleHttp\Client();
$resp = $client->post("http://www.example.com/create_post", array(
    "json" => array(
        "title" => "Vote for Pedro",
        "body"  => "If you vote for Pedro, your wildest dreams will come true"
    )
));
```

{% endblock %}

{% block upload_file_special_middleware %}
{% endblock %}

{% block code_upload_file_sdk_function %}

```php
$app->post("/upload", function($req, $res) {
    if (isset($_FILES["iconImage"]) && $_FILES["iconImage"]["size"] != 0) {
        $file = File::createWithLocalFile(
            $_FILES["iconImage"]["tmp_name"],
            $_FILES["iconImage"]["type"]
        );
        $file->save();
        $res->getBody()->write("File upload succeeded");
    } else {
        $res->getBody()->write("Please select a file");
    }
});
```
{% endblock %}

{% block cookie_session %}
云引擎提供了一个 `LeanCloud\Storage\CookieStorage` 模块，用 Cookie 来维护用户（`User`）的登录状态，要使用它可以在 `app.php` 中添加下列代码：
LeanEngine provides a module `LeanCloud\Storage\CookieStorage` that uses Cookie to manage the login of the （`User`）. You can use it by adding the following code to `app.php`.
```php
use \LeanCloud\Storage\CookieStorage;
// 将会话状态存储到 cookie 中 store the conversation status to cookie
Client::setStorage(new CookieStorage(60 * 60 * 24, "/"));
```

CookieStorage 支持传入秒作为过期时间, 以及路径作为 cookie 的作用域。默认过期时间为 7 天。然后我们可以通过 `User::getCurrentUser()` 来获取当前登录用户。

你可以这样简单地实现一个具有登录功能的站点：

```php
$app->get('/login', function($req, $res) {
  // 渲染登录页面
});

// 处理登录请求（可能来自登录界面中的表单）
$app->post('/login', function($req, $res) {
    $params = $req->getQueryParams();
    try {
        User::logIn($params["username"], $params["password"]);
        // 跳转到个人资料页面
        return $res->withRedirect('/profile');
    } catch (Exception $ex) {
        //登录失败，跳转到登录页面
        return $res->withRedirect('/login');
    }
});

// 查看个人资料
$app->get('/profile', function($req, $res) {
    // 判断用户是否已经登录
    $user = User::getCurrentUser();
    if ($user) {
        // 如果已经登录，发送当前登录用户信息。
        return $res->getBody()->write($user->getUsername());
    } else {
        // 没有登录，跳转到登录页面。
        return $res->withRedirect('/login');
    }
});

// 登出账号
$app->get('/logout', function($req, $res) {
    User::logOut();
    return $res->redirect("/");
});
```

一个简单的登录页面可以是这样：

```html
<html>
    <head></head>
    <body>
      <form method="post" action="/login">
        <label>Username</label>
        <input name="username"></input>
        <label>Password</label>
        <input name="password" type="password"></input>
        <input class="button" type="submit" value="登录">
      </form>
    </body>
  </html>
```

{% endblock %}

{% block custom_session %}
如果你需要将一些属性保存在 session 中，可以增加通用的 CookieStorage 来保存：

```php
// 在项目启动时启用 CookieStorage
Client::setStorage(new CookieStorage());

// 在项目中可以使用 CookieStorage 存储属性
$cookieStorage = Client::getStorage();
$cookieStorage->set("key", "val");
```

注意：PHP 默认的 `$_SESSION` 在我们云引擎中是无法正常工作的，因为我们
的云引擎是多主机、多进程运行，因此内存型 session 是无法共享的。建议用
`CookieStorage` 来存储会话信息。

{% endblock %}

{% block https_redirect %}
```php
SlimEngine::enableHttpsRedirect();
$app->add(new SlimEngine());
```
{% endblock %}


{% block custom_runtime %}
云引擎默认提供 PHP 5.6 的运行环境，如需指定 PHP 版本，请在 `composer.json` 中添加：

```json
"require": {
  "php": "7.0"
}
```

目前云引擎支持 `5.6`、`7.0`、`7.1` 这几个版本，后续如果有新版本发布，也会添加支持。

{% endblock %}

{% block get_env %}
```php
$env = getenv("LEANCLOUD_APP_ENV");
if ($env === "development") {
    // 当前环境为「开发环境」，是由命令行工具启动的
} else if ($env === "production") {
    // 当前环境为「生产环境」，是线上正式运行的环境
} else {
    // 当前环境为「预备环境」
}
```
{% endblock %}

{% block loggerExample %}
```php
Cloud::define("logSomething", function($params, $user) {
    error_log(json_encode($params));
});
```
{% endblock %}

{% block section_timezone %}{% endblock %}