---
title: "Setting the Default Character Set and Collation to UTF-8 Properly in MySQL"
publishdate: "2018-05-06T15:28:00+08:00"
type: "post"
slug: "mysql-utf8mb4"
isCJKLanguage: true
---

由於 MySQL 8.0 預設的 collation 是 `utf8mb4_0900_ai_ci` 而不是我原先以為「最精準」的 `utf8mb4_unicode_ci`，讓我好奇而查了一下，發現[^1][^2]：

* `0900` 代表 Unicode Collation Algorithm (UCA) 的 9.0.0 版本，包含在 2016 年發佈的 Unicode 9.0.0 之中[^3]。目前最新的版本是 11.0.0[^4]
* `ai` 代表 accent insensitivity，即同一字母的不同標音不影響排序
* `ci` 就是 case insensitivity 了，同一字母的大小寫不影響排序

所以，不管是從其他方面，或是我所認為的「精準」來說，`utf8mb4_0900_ai_ci` (UCA 9.0.0) 都比 `utf8mb4_unicode_ci` (UCA 4.0.0) 要來得好。甚至是在 MySQL 5.7.7 裡，也都有 `utf8mb4_unicode_520_ci` (UCA 5.2.0) 這個更好的選擇[^5]

結論就是，在 MySQL 中，設定 UTF-8 編碼的最佳組合是：

* 5.5.3+: `charset=utf8mb4, collation=utf8mb4_unicode_ci`
* 5.7.7+: `charset=utf8mb4, collation=utf8mb4_unicode_520_ci`
* 8.0.0+: `charset=utf8mb4, collation=utf8mb4_0900_ai_ci`

順便一提，`utf8mb4` 這個 charset 是在 MySQL 5.5.3 加入的[^6]。在此之前，只有 `utf8`（5.5.3 之後等義於 `utf8mb3`）可用

[^1]: [MySQL :: MySQL 8.0 Reference Manual :: 10.3.1 Collation Naming Conventions](https://dev.mysql.com/doc/refman/8.0/en/charset-collation-names.html)
[^2]: [What is the utf8mb4_0900_ai_ci Collation? - Monolune](https://www.monolune.com/what-is-the-utf8mb4_0900_ai_ci-collation/)
[^3]: [Unicode 9.0.0](http://unicode.org/versions/Unicode9.0.0/)
[^4]: [The Unicode Blog: Last Call on Unicode 11.0 Review](http://blog.unicode.org/2018/04/last-call-on-unicode-110-review.html)
[^5]: [mysql - Why is table CHARSET set to utf8mb4 and COLLATION to utf8mb4_unicode_520_ci - Stack Overflow](https://stackoverflow.com/a/43692337)
[^6]: [MySQL :: MySQL 5.5 Release Notes :: Changes in MySQL 5.5.3 (2010-03-24, Milestone 3)](https://dev.mysql.com/doc/relnotes/mysql/5.5/en/news-5-5-3.html)
