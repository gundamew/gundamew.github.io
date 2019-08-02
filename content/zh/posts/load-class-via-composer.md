---
title: "Loading Classes without Namespace via Composer"
publishdate: "2017-10-28T14:24:00+08:00"
type: "post"
slug: "load-class-via-composer"
isCJKLanguage: true
---

沒有設定 namespace 或沒有依照 PSR-4 規範的 PHP class 檔案，依然可以透過 Composer 載入。以綠界的 SDK [ECPay/ECPayAIO_PHP](https://github.com/ECPay/ECPayAIO_PHP) 為例，在 `composer.json` 中加入下列設定：

```json
{
    "autoload": {
        "classmap": [
            "vendor/ECPay/ECPayAIO_PHP/AioSDK/sdk"
        ]
    },
    "repositories": [
        {
            "type": "package",
            "package": {
                "name": "ecpay/ecpayaio_php",
                "version": "dev-master",
                "source": {
                    "url": "git@github.com:ECPay/ECPayAIO_PHP.git",
                    "type": "git",
                    "reference": "dev-master"
                },
                "dist": {
                    "url": "https://github.com/ECPay/ECPayAIO_PHP/archive/master.zip",
                    "type": "zip"
                }
            }
        }
    ]
}
```

⚠️ `autoload.classmap` 那裡，因為 `vendor/ECPay/ECPayAIO_PHP/ApplePaySDK` 和 `vendor/ECPay/ECPayAIO_PHP/AioSDK` 使用了相同的 method 名稱，又沒有設定 namespace。所以如果想把路徑設為 `vendor/ECPay/ECPayAIO_PHP` 一次載入所有的 class 的話，會因為 method 名稱重複產生衝突。把 `ApplePaySDK` 和 `AioSDK` 路徑分開列入 `autoload.classmap` 也沒用，目前尚未找到解法

⚠️ `repositories.package` 底下，`source` 和 `dist` 根據 Composer 的安裝偏好 `preferred-install` 的設定，擇一使用即可。為了操作靈活，我就二邊都設定了

最後在使用 class 時，記得在開頭加上反斜線 `\` 註明是 global class

```php
$ecpay = new \ECPay_AllInOne();
```

參考資料：

* [Repositories - Composer](https://getcomposer.org/doc/05-repositories.md#packages)
* [The composer.json Schema - Composer](https://getcomposer.org/doc/04-schema.md#repositories)
* [composer 載入 github project | Hit FM 94.87](https://blog.gotobye.com/coding/composer-from-git-project/)
* [php - How do you get composer to install a non-composer package? - Stack Overflow](https://stackoverflow.com/a/16873912)
