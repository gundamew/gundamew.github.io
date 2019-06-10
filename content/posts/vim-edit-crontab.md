---
title: "Edit crontab with Vim on macOS"
date: "2016-06-16 12:00:00"
type: "post"
slug: "vim-edit-crontab"
isCJKLanguage: true
---

在 macOS 上用 Vim 編輯 cron 時，除了會儲存失敗，還會收到下面的錯誤訊息：

> crontab: temp file must be edited in place

一開始我以為是 Apple 不使用 crontab 而只用自家的 launchd。但看了文件之後發現 macOS 上的 crontab 並沒有被廢棄，我才把矛頭指向 Vim

找解法找了很久，最後才靠著某篇回答，在 `.vimrc` 裡加上一列設定處理掉這個狀況[^1]：

```vim
set nowritebackup
```

它的起因是 Vim 對「修改檔案」這件事的處理方式，更精確地說是 `backupcopy` 這個選項

檢查 [`:set backupcopy`](http://vimdoc.sourceforge.net/htmldoc/options.html#'backupcopy') 的內容可以發現，當其值為 `yes` 時，Vim 會先複製一份原始檔案之後，才修改原始檔案；而其值為 `no` 時，則會直接把原始檔案改名，再建立新檔案

從 [`:set crontab`](http://vimdoc.sourceforge.net/htmldoc/options.html#crontab) 的內容來看，當 `set backupcopy=no` 時，可能會發生二種情況：原始檔案其實是一個 link 而 Vim 只能建立一個新檔案而非新的 link；或者因為權限的關係，Vim 無法建立新檔案。這二種情況都會導致寫入失敗

非 Unix 系統上的 Vim 的這個選項預設值是 `auto`，所以要再另外設定為 `yes` 才能避開這個狀況。當然也可以直接把 backup 選項給關掉

2017-10-24 edit: 事隔一年多後，又在網路上看到有人也踩到一樣的問題[^2]

不同的是，對方找到的解法跟我找到的解法不太一樣。對方所找到的文章裡[^3]所給的解法是：

```vim
autocmd filetype crontab setlocal nobackup nowritebackup
```

[^1]: https://www.plurk.com/p/lonkxt
[^2]: https://www.plurk.com/p/mh5n8g
[^3]: http://vim.wikia.com/wiki/Editing_crontab
