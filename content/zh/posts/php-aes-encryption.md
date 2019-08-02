---
title: "An AES Encryption Implementation in PHP"
date: "2016-06-01 14:52:00"
type: "post"
slug: "php-aes-encryption"
isCJKLanguage: true
---

雖然網路上找到的大部份程式碼都是用 Mcrypt extension 實作的範例，不過用 OpenSSL extension 來實作的話會更簡潔明瞭：

https://gist.github.com/gundamew/3462de49997eeb1fe34753d6c27f7646

用 Mcrypt 實作的範例：

https://gist.github.com/gundamew/2f3705db4c421f1ddd2655a72697abc4

實作時還發生了一個小烏龍：由於跟別人事先約定好要用 AES 128 加密，可是共用的密鑰長度卻是 32 個字元（256 bits），讓我在用 `AES-128-CBC` 測試時一直沒辦法解密出正確的原文，之後心血來潮改用 `AES-256-CBC` 測試後就成功解密了。找資料時看到[別人遇到的類似狀況](http://stackoverflow.com/a/4413290/6404056)，我才確定問題出在密鑰的長度上

其他參考資料：

* [Encrypt and decrypt strings in PHP | Urban Insight](https://www.urbaninsight.com/2012/06/13/encrypt-and-decrypt-strings-php)
* [Using openssl_en/decrypt() in PHP instead of...](http://thefsb.tumblr.com/post/110749271235/using-opensslendecrypt-in-php-instead-of)
* [initialisation vector - Encrypting using AES-256, can I use 256 bits IV? - Information Security Stack Exchange](http://security.stackexchange.com/a/90850)
* [寫程式是良心事業: Python M2Crypto - AES 的 Encrypt 與 Decrypt](http://ijecorp.blogspot.tw/2013/08/python-m2crypto-aes-encrypt-decrypt.html)
