---
title: "ISO Week Date: A Case Study of Twitter Down"
date: 2014-12-29
draft: false
slug: "iso-week-date-a-case-study-of-twitter-down"
---

由於**本週四**就是 2015 年的元旦了，所以用 [ISO week date](https://en.wikipedia.org/wiki/ISO_week_date) 記錄時間的系統，會從**週一**開始進入 2015 年，即使週一的日期是 2014-12-29 才對

摘自 Wikipedia 的說明：

> The first week of the year, hence, always [contains 4 January](https://en.wikipedia.org/wiki/ISO_week_date#First_week). ISO week [year numbering](https://en.wikipedia.org/wiki/Year_numbering) therefore slightly deviates from the Gregorian for some days close to 1 January.

關於 PHP [`date('o')`](http://php.net/manual/en/function.date.php) 的說明：

> ISO-8601 year number. This has the same value as *Y*, except that if the ISO week number (*W*) belongs to the previous or next year, that year is used instead. (added in PHP 5.1.0)

自己測試的結果：
```php
// 2015
echo date('o');

// 2014
echo date('Y');
```

然後，從今早的傳言看來，Twitter 的 OAuth server 出的包可能就是因為這個……（原文已刪，連結是 https://www.facebook.com/thehackernews/posts/1051721444841728）

## 2014-12-31 edit
從 [Hacker News](https://news.ycombinator.com/item?id=8810157) 的消息看來，Twitter 掛站確定是這個原因了：

> If you're using YYYY in your JVM service or %G in anything, fix it now
>
> The single character G instead of Y took down Twitter's API today.
> If you're using YYYY in your JVM service or %G in anything, fix it now. You're very likely using the wrong year format.
>
> date
> Mon Dec 29 00:44:45 EST 2014
>
> date -u "+%G"
> 2015
>
> ISO 8601 week numbering has 2015 start this week.

自己在 Bash 裡下指令的結果：
```bash
$ date +%Y
2014

$ date +%G
2015

$ date --help
%Y   year
%G   year of ISO week number (see %V); normally useful only with %V
%V   ISO week number, with Monday as first day of week (01..53)
```
