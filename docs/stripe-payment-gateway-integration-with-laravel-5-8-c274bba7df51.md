# 条带支付网关与 Laravel 5.8 集成。

> 原文：<https://medium.com/codex/stripe-payment-gateway-integration-with-laravel-5-8-c274bba7df51?source=collection_archive---------10----------------------->

![](img/18ea2c2c8e79a59ccce342d5ec6b42a8.png)

问候大家，我是 Shivasish Dey。在本文中，我们将了解如何将 **Stripe 支付网关**与 Laravel 5.8 项目集成。

我们先来简单了解一下**条纹**。它允许企业主在全球范围内接受信用卡和借记卡支付，并处理这些支付。企业也可以接受来自手机钱包的支付，现在购买，以后支付服务。Stripe 还提供了一种测试模式，开发人员可以使用这种模式进行测试。

# **要求**

1.  开发人员的 Stripe 帐户。
2.  可发布密钥和秘密密钥，您可以在登录后在主页选项卡中获得。

**第一步:安装 Laravel 5.8**

让我们从头开始，为了安装 Laravel 5.8，我们将使用下面的命令。打开命令提示符，导航到“htdocs”文件夹。

```
composer create-project — prefer-dist laravel/laravel blog
```

**第二步:安装 stripe-php 包**

使用 composer 包管理器，我们必须运行下面的命令来安装 stripe-php 包。

```
composer require stripe/stripe-php
```

**步骤 3:设置条纹可公开密钥和秘密密钥**

现在，我们必须设置可发布密钥和秘密密钥。登录 Stripe 网站后，我们可以在主页上找到可公开密钥和秘密密钥。

```
STRIPE_KEY=pk_test_txxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
STRIPE_SECRET=sk_test_exxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

将上述两个键复制粘贴到“**”中。env** 文件。

**步骤 4:创建路线**

```
Route::get('stripe', 'StripePaymentController@stripe');
Route::post('stripe', 'StripePaymentController@stripePost')->name('stripe.post');
```

**第五步:创建控制器**

我建议您为 Stripe 创建一个单独的控制器文件，以避免进一步的混淆。

*app/Http/Controllers/stripepaymentcontroller . PHP*

```
**namespace** App\Http\Controllers;**use** Illuminate\Http\Request;
**use** Session;
**use** Stripe;**class** StripePaymentController **extends** Controller{ /**
   * success response method.
   *
   * @return \Illuminate\Http\Response
   */ **public** **function** stripe(){ **return** view('stripe'); } /**
   * success response method.
   *
   * @return \Illuminate\Http\Response
   */ **public** **function** stripePost(Request $request){ Stripe\Stripe::setApiKey(env('STRIPE_SECRET')); Stripe\Charge::create ([ "amount" => 100 * 100,
         "currency" => "usd",
         "source" => $request->stripeToken,
         "description" => "Test payment from itsolutionstuff.com."

      ]); Session::flash('success', 'Payment successful!');

      **return** back();
   }}
```

**步骤 6:在前端工作(刀片文件)**

我们的前端文件将包含从 Stripe 获取令牌的 jQuery。

*resources/views/stripe . blade . PHP*

```
<!DOCTYPE html>
<html>
<head>
 <title>Laravel 5 - Stripe Payment Gateway Integration Example - ItSolutionStuff.com</title>
 <link rel="stylesheet" href="[https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.7/css/bootstrap.min.css](https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.7/css/bootstrap.min.css)" />
    <script src="[https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js](https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js)"></script>
    <style type="text/css">
        .panel-title {
        display: inline;
        font-weight: bold;
        }
        .display-table {
            display: table;
        }
        .display-tr {
            display: table-row;
        }
        .display-td {
            display: table-cell;
            vertical-align: middle;
            width: 61%;
        }
    </style>
</head>
<body>

<div class="container">

    <div class="row">
        <div class="col-md-6 col-md-offset-3">
            <div class="panel panel-default credit-card-box">
                <div class="panel-heading display-table" >
                    <div class="row display-tr" >
                        <h3 class="panel-title display-td" >Payment Details</h3>
                        <div class="display-td" >                            
                            <img class="img-responsive pull-right" src="[https://openjournalsystems.com/file/2017/07/payment-success.png](https://openjournalsystems.com/file/2017/07/payment-success.png)">
                        </div>
                    </div>                    
                </div>
                <div class="panel-body">

                    [@if](http://twitter.com/if) (Session::has('success'))
                        <div class="alert alert-success text-center">
                            <a href="#" class="close" data-dismiss="alert" aria-label="close">×</a>
                            <p>{{ Session::get('success') }}</p>
                        </div>
                    [@endif](http://twitter.com/endif)

                    <form role="form" action="{{ route('stripe.post') }}" method="post" class="require-validation"
                                                     data-cc-on-file="false"
                                                    data-stripe-publishable-key="{{ env('STRIPE_KEY') }}"
                                                    id="payment-form">
                        [@csrf](http://twitter.com/csrf)

                        <div class='form-row row'>
                            <div class='col-xs-12 form-group required'>
                                <label class='control-label'>Name on Card</label> <input
                                    class='form-control' size='4' type='text'>
                            </div>
                        </div>

                        <div class='form-row row'>
                            <div class='col-xs-12 form-group card required'>
                                <label class='control-label'>Card Number</label> <input
                                    autocomplete='off' class='form-control card-number' size='20'
                                    type='text'>
                            </div>
                        </div>

                        <div class='form-row row'>
                            <div class='col-xs-12 col-md-4 form-group cvc required'>
                                <label class='control-label'>CVC</label> <input autocomplete='off'
                                    class='form-control card-cvc' placeholder='ex. 311' size='4'
                                    type='text'>
                            </div>
                            <div class='col-xs-12 col-md-4 form-group expiration required'>
                                <label class='control-label'>Expiration Month</label> <input
                                    class='form-control card-expiry-month' placeholder='MM' size='2'
                                    type='text'>
                            </div>
                            <div class='col-xs-12 col-md-4 form-group expiration required'>
                                <label class='control-label'>Expiration Year</label> <input
                                    class='form-control card-expiry-year' placeholder='YYYY' size='4'
                                    type='text'>
                            </div>
                        </div>

                        <div class='form-row row'>
                            <div class='col-md-12 error form-group hide'>
                                <div class='alert-danger alert'>Please correct the errors and try
                                    again.</div>
                            </div>
                        </div>

                        <div class="row">
                            <div class="col-xs-12">
                                <button class="btn btn-primary btn-lg btn-block" type="submit">Pay Now ($100)</button>
                            </div>
                        </div>

                    </form>
                </div>
            </div>        
        </div>
    </div>

</div>

</body>

<script type="text/javascript" src="[https://js.stripe.com/v2/](https://js.stripe.com/v2/)"></script>

<script type="text/javascript">
$(function() {
    var $form         = $(".require-validation");
  $('form.require-validation').bind('submit', function(e) {
    var $form         = $(".require-validation"),
        inputSelector = ['input[type=email]', 'input[type=password]',
                         'input[type=text]', 'input[type=file]',
                         'textarea'].join(', '),
        $inputs       = $form.find('.required').find(inputSelector),
        $errorMessage = $form.find('div.error'),
        valid         = true;
        $errorMessage.addClass('hide');

        $('.has-error').removeClass('has-error');
    $inputs.each(function(i, el) {
      var $input = $(el);
      if ($input.val() === '') {
        $input.parent().addClass('has-error');
        $errorMessage.removeClass('hide');
        e.preventDefault();
      }
    });

    if (!$form.data('cc-on-file')) {
      e.preventDefault();
      Stripe.setPublishableKey($form.data('stripe-publishable-key'));
      Stripe.createToken({
        number: $('.card-number').val(),
        cvc: $('.card-cvc').val(),
        exp_month: $('.card-expiry-month').val(),
        exp_year: $('.card-expiry-year').val()
      }, stripeResponseHandler);
    }

  });

  function stripeResponseHandler(status, response) {
        if (response.error) {
            $('.error')
                .removeClass('hide')
                .find('.alert')
                .text(response.error.message);
        } else {

            var token = response['id'];

            $form.find('input[type=text]').empty();
            $form.append("<input type='hidden' name='stripeToken' value='" + token + "'/>");
            $form.get(0).submit();
        }
    }

});
</script>
</html>
```

**第七步:测试数据**

名称:

卡号:4242 4242 4242 4242

CVV: 123 人

到期月份:12

有效期:2024 年

祝你好运👍