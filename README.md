## 秒支付Button

![license](https://img.shields.io/badge/license-MIT-brightgreen.svg) ![v1.1.0](https://img.shields.io/badge/Version-v1.1.0-blue.svg)  

本项目的官方GitHub地址是 [https://github.com/beecloud/beecloud-button](https://github.com/beecloud/beecloud-button)

## 简介

用户通过在Javascript中调用秒支付Button的**BC.click**方法实现“发起支付”的功能，目前支持支付宝、银联、微信支付、百度钱包、京东支付和易宝，并兼容了PC端页面和移动端H5页面，使用效果请参考[在线示例](https://beecloud.cn/activity/jsbutton/?index=4)


## 使用效果
用户在调用BC.click后(比如用户在DOM的click事件中调用BC.click), 秒支付Button的表现形式分为： 

1\. （默认）网页上出现支付渠道选择菜单，点击其中渠道跳转到指定渠道的支付页面，WEB和WAP端效果分别如下图：

   PC端如下：
        
![Button GIF](http://7xavqo.com1.z0.glb.clouddn.com/button2.gif)

   移动H5端如下：
   
![Button GIF](http://7xavqo.com1.z0.glb.clouddn.com/button_wap.gif)

2\. 网页直接跳转到指定渠道的支付页面，这需要设置选填的**instant_channel**参数，见**BC.click接口说明**中的描述


## 使用前准备

### BeeCloud配置
1. BeeCloud[注册](http://beecloud.cn/register/)账号, 并完成企业认证

2. BeeCloud中创建应用，填写支付渠道所需参数, 可以参考[官网帮助文档](http://beecloud.cn/doc/payapply)

3. 申请渠道参数，并配置BeeCLoud各个支付渠道的参数，此处请参考官网的[渠道参数帮助页](https://beecloud.cn/doc/payapply/?index=0)
>BeeCloud中配置参数需要完成企业认证后才能填写!

4. 激活秒支付button功能，进入APP->设置->秒支付button项，拖拽支付渠道开启该支付渠道，同时还可以调整你需要的渠道菜单的显示顺序，点击”保存“后会生成appid对应的**script标签**。**需要将此script标签放到任何需要使用秒支付Button的网页里**。

![支付设置前](http://beeclouddoc.qiniudn.com/1111.png)

## BC.click接口说明
### BC.click原型
	
~~~
BC.click(data, event);
~~~	

### 必填参数data字段说明
参数名 | 类型 | 含义 | 限制| 例子 | 是否必填
----  | ---- | ---- | ---- | ---- | ----
out\_trade\_no | String | 支付订单的编号 | 全局唯一,8到32位的**字母和/或数字**组合 | bc1234567 | 是
title | String | 支付订单的标题 | 小于16汉字或者32个字符,不支持英文的单引号 **'** 与双引号 **"** | 你的订单 | 是
amount | Int | 支付订单的总价(单位:分) | 大于0 | 1 | 是
sign | String | 订单信息安全签名 |  依次将以下字段（注意是UTF8编码）连接BeeCloud appId、 title、 amount、 out\_trade\_no、 BeeCloud appSecret, 然后计算连接后的字符串的32位MD5 | b6273d932b0aa801d9bd97220f1fb039 | 是
return\_url | String | 支付成功后跳转地址，除微信内jsapi支付不支持 | 必须以http://或https://开头, **不允许带任何参数** | http://www.beecloud.cn | 否
debug | bool | 调试信息开关, 开启后将alert一些信息 | 默认为false | false | 否
optional | Object | 支付完成后，webhook将收到的自定义订单相关信息 | 目前只支持javascript基本类型的{key:value}, 不支持嵌套对象 | ｛"msg":"hello world"｝| 否
instant\_channel | String | 设置该字段后将直接调用渠道支付，不再显示渠道选择菜单 | 必须为"ali", "wxmp"(native扫码), "wx"(jsapi网页内支付), "un"中的一个 | "ali" | 否
need\_ali\_guide | bool | 微信内是否使用支付宝支付引导页，若不使用设置false | 默认为true | true | 否
openid | String | 微信内调JSAPI支付必须参数 | 需要开发者微信网页授权获取 | o3kKrjlUsMnv__cK5DYZMl0JoAkY | 否
use_app | bool | 移动端网页直接调用支付宝APP支付，默认true，传false不启用 | 仅限支付宝移动网页端起作用 | 否

### 选填参数event说明

>注意只有在支付授权目录下支付时，微信才会调用jsapi中注册的函数；
>测试授权目录下的支付不会出发wxJsapiFinish等事件
>有关微信jsapi的返回结果res,请参考[这里](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=7_7)

参数名 | 类型 | 描述 | 是否必填
----  | ---- | ---- | ----
dataError | function(msg) | 数据获取出错,将调用此接口; 只传递一个参数为Object,其中有错误描述 | 否
wxJsapiFinish | function(res) | 微信jsapi的支付接口调用完成后将调用此接口; 只传递一个参数，为微信原生的结果Object | 否
wxJsapiSuccess | function(res) | 微信jsapi的接口支付成功后将调用此接口; 只传递一个参数，为微信原生的结果Object | 否
wxJsapiFail | function(res) | 微信jsapi的接口支付非成功都将调用此接口; 只传递一个参数，为微信原生的结果Object | 否


##在支付页面集成代码

### 参考Demo
本项目里给出的几种语言的Demo实现，仅供参考：
[Demo 目录](https://github.com/beecloud/beecloud-button/tree/master/demo)

### 示例步骤
若为移动端H5页面，页面头部需加上```<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">```做移动适配。

以下为PHP的代码示例,Javascript传递的参数中sign比较特殊，用来保证订单的信息的完整性，需要集成者自行在服务器端生成；

生成规则 : 依次将以下字段（注意是UTF8编码）连接BeeCloud appId、 title、 amount、 out\_trade\_no、 BeeCloud appSecret, 然后计算连接后的字符串的MD5, 该签名用于验证价格，title 和订单的一致

```
<?php
$app_id = "c5d1cba1-5e3f-4ba0-941d-9b0a371fe719";
$app_secret = "39a7a518-9ac8-4a9e-87bc-7885f33cf18c";
$title = "你的订单标题";
$amount = 1;//支付总价
$out_trade_no = "bc" . time();//订单号，需要保证唯一性
//1.生成sign
$sign = md5($app_id . $title . $amount . $out_trade_no . $app_secret);
?>
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <title>demo js button</title>
</head>
<body>
<button id="test">test online</button>


<!--2.添加控制台->APP->设置->秒支付button项获得的script标签-->
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
<script>
    //3. 需要发起支付时(示例中绑定在一个按钮的click事件中),调用BC.click方法
    document.getElementById("test").onclick = function() {
        asyncPay();
    };
    function bcPay() {
        BC.click({
            "title": "<?php echo $title; ?>",
            "amount": <?php echo $amount; ?>,
            "out_trade_no": "<?php echo $out_trade_no;?>", //唯一订单号
            "sign" : "<?php echo $sign;?>",
            /**
             * optional 为自定义参数对象，目前只支持基本类型的key ＝》 value, 不支持嵌套对象；
             * 回调时如果有optional则会传递给webhook地址，webhook的使用请查阅文档
             */
            "optional": {"test": "willreturn"}
        });
    }
    // 这里不直接调用BC.click的原因是防止用户点击过快，BC的JS还没加载完成就点击了支付按钮。
    // 实际使用过程中，如果用户不可能在页面加载过程中立刻点击支付按钮，就没有必要利用asyncPay的方式，而是可以直接调用BC.click。
    function asyncPay() {
        if (typeof BC == "undefined") {
            if (document.addEventListener) { // 大部分浏览器
                document.addEventListener('beecloud:onready', bcPay, false);
            } else if (document.attachEvent) { // 兼容IE 11之前的版本
                document.attachEvent('beecloud:onready', bcPay);
            }
        } else {
            bcPay();
        }
    }
</script>
</body>
</html>
```

### 微信JSAPI示例

微信JSAPI支付使用之前，需要配置相应的参数，主要包括：授权回调页面域名、支付授权目录、API安全密钥等，详情请参考[快速开始-渠道申请及参数配置帮助](https://beecloud.cn/doc/payapply/?index=3)

微信内使用网页JSAPI支付比较特殊，需要自行获取用户的openid，微信提供了各语言的封装的[函数库(点击查看)](https://pay.weixin.qq.com/wiki/doc/api/native.php?chapter=11_1), BeeCloud各语言SDK下微信JSAPI的DEMO中也可以作为参考。

>dependency/WxPayPubHelper/WxPayPubHelper.php内需要配置为你的微信appId和Secret才能正确使用

以下为php在微信网页内使用秒支付button的示例,请仔细理解获取openid的过程：

~~~
<?php
include_once('dependency/WxPayPubHelper/WxPayPubHelper.php');

$jsApi = new JsApi_pub();
//网页授权获取用户openid
//通过code获得openid
$openid = "";
try {
    if (!isset($_GET['code'])) {
        //触发微信返回code码
        $url = $jsApi->createOauthUrlForCode("你的微信网页地址");
        Header("Location: $url");
    } else {
        //获取code码，以获取openid
        $code = $_GET['code'];
        $jsApi->setCode($code);
        $openid = $jsApi->getOpenId();
    }
} catch (Exception $e) {
    echo $e->getMessage();
    exit();
}
?>
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black">
    <meta content="telephone=no" name="format-detection">
    <meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no">
    <title>demo js pay</title>
    <link rel="stylesheet" href="../src/css/api.css" >
</head>
<body>
<button id="test" style="position: absolute; left: 10px; top: 10px; z-index: 99999;">test</button>
<div id="qr"></div>
<script id='spay-script' type='text/javascript' src='https://jspay.beecloud.cn/1/pay/jsbutton/returnscripts?appId=c5d1cba1-5e3f-4ba0-941d-9b0a371fe719'></script>
<?php
$data = array(
    "app_id" =>  "c5d1cba1-5e3f-4ba0-941d-9b0a371fe719",
    "title" => "test",
    "amount" => "1",
    "out_trade_no" => "test".time(),
    "openid" => $openid
);

$app_secret = "39a7a518-9ac8-4a9e-87bc-7885f33cf18c";
$sign = md5($data['app_id'] . $data['title'] . $data['amount'] . $data['out_trade_no'] . $app_secret);
$data["sign"] = $sign;
$data["optional"] = json_decode(json_encode(array("hello" => "1")));
//    $data["openid"] ="o3kKrjlUsMnv__cK5DYZMl0JoAkY";   //o3kKrjlUsMnv__cK5DYZMl0JoAkY   oOCyauJ6nKcXiIIQ_bixiQpaL6PQ(me)
?>

<div><?php echo json_encode($data) ?></div>
<script>
    document.getElementById("test").onclick = function() {
        asyncPay();
    };

    function bcPay() {
        BC.click(<?php echo json_encode($data) ?>, {
            wxJsapiFinish : function(res) {
                //jsapi接口调用完成后
                alert(JSON.stringify(res));
            }
        });
    }
    
    // 这里不直接调用BC.click的原因是防止用户点击过快，BC的JS还没加载完成就点击了支付按钮。
    // 实际使用过程中，如果用户不可能在页面加载过程中立刻点击支付按钮，就没有必要利用asyncPay的方式，而是可以直接调用BC.click。
    function asyncPay() {
        if (typeof BC == "undefined") {
            if (document.addEventListener) { // 大部分浏览器
                document.addEventListener('beecloud:onready', bcPay, false);
            } else if (document.attachEvent) { // 兼容IE 11之前的版本
                document.attachEvent('beecloud:onready', bcPay);
            }
        } else {
            bcPay();
        }
    }
</script>
</body>
</html>
~~~

##处理支付结果
各支付渠道通常会提供多种方式获取支付结果：

1. (建议不要用)客户的支付页面在支付成功后跳转到商户指定的的url(秒支付 Button统一包装为return_url参数), 但是此方式受到客户操作影响,可能不成功。
2. 商户向指定渠道查询订单支付状态, 但是各个渠道支持情况不一样，有的有查询接口有的没有。
3. 支付渠道在支付成功后，将相关通知（俗称’回调‘）商户指定的url（BeeCloud统一封装为webhook并且统一支持用户自定义回调参数’optional‘）

建议使用webhook作为处理支付结果方式，使用请参考[webhook指南](https://github.com/beecloud/beecloud-webhook)
