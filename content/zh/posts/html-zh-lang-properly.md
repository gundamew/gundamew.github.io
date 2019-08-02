---
title: "Declaring Chinese Language in HTML Properly"
publishdate: "2017-11-23T11:19:00+08:00"
type: "post"
slug: "html-zh-lang-properly"
isCJKLanguage: true
---

自己在意了很久，但都沒有認真下個結論的問題：在臺灣的正體中文網頁裡，到底 `<html lang="">` 的值該填什麼？

中文的問題應該找中文的討論吧……但是搜尋到的都只是轉貼文章，或是「我認為」但沒有佐證的說法

只有這個知乎討論看起來靠譜些：[网页头部的声明应该是用 lang="zh" 还是 lang="zh-cn"？ - 知乎](https://www.zhihu.com/question/20797118)

我從裡面揀了幾個答案來看：

* [贺师俊的回答](https://www.zhihu.com/question/20797118/answer/16809331)
* [猎奇怪蜀黍的回答](https://www.zhihu.com/question/20797118/answer/63480740)
* [sinkcup 的回答](https://www.zhihu.com/question/20797118/answer/81543429)

然後稍微瀏覽了一下答案裡提到的規格書：

[3 Semantics, structure, and APIs of HTML documents — HTML5](https://www.w3.org/TR/html5/dom.html#attr-lang)
> Its value must be a valid BCP 47 language tag, or the empty string.

http://www.ietf.org/rfc/bcp/bcp47.txt

從 BCP 47 看來，好像用 `zh-Hant` 就夠了？

https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry

IANA 的文件指明，比起 `zh-cmn-Hant` 更建議使用 `cmn-Hant`。而 `zh-Hant` 和 `zh-Hant-TW` 都被標為 redundant

所以目前我的結論是：用 `cmn-Hant`，然後為了相容再加上前綴 `zh-` 變成 `zh-cmn-Hant` 即可。後綴 `-TW` 可不加，除非是要特別標出與其他地方中文的不同之處，例如「土豆（`cmn-Hans-CN`）」和「馬鈴薯（`cmn-Hant-TW`）」

語言分類與定義的規範就不再列在這裡，而這也只是針對文字部份的筆記而已，聲音的部份暫不研究
