# coc.nvim 完全指南

用户配置：~/.config/nvim/coc-settings.json
```javascript
{
  // 设置要在保存时格式化的语言
  "coc.preferences.formatOnSaveFiletypes": [
    "javascript", "typescript",
    "javascriptreact", "typescriptreact", "typescript.tsx",
    "css", "less", "scss",
    "html", "vue"
    "json",
  ],
  "eslint.filetypes": ["javascript", "typescript", "typescriptreact", "javascriptreact", "typescript.tsx", "scss", "vue"],
  "coc.preferences.diagnostic.virtualText": true,

  // 禁用 prettier 的语言列表
  "prettier.disableLanguages": [],
  // 一行最多 120 字符
  "prettier.printWidth": 120,
  // 使用单引号
  "prettier.singleQuote": true,
  // 末尾需要有逗号
  "prettier.trailingComma": 'all',
  // 箭头函数，只有一个参数的时候，也需要括号
  "prettier.arrowParens": 'always',

  // 保存时自动修复
  "eslint.autoFixOnSave": true
}
```

注意：可以在打开的某个文件中运行 :echo &filetype 确定当前文件类型；之后如果更新了配置中支持的文件类型可能需要重新运行 :CocInstall coc-eslint 以升级扩展

## coc-prettier

coc-prettier 可以对代码进行格式化， 默认的快捷键是：热键 + f， 支持全篇格式化和仅格式化选中
```
vmap <leader>f  <Plug>(coc-format-selected)
nmap <leader>f  <Plug>(coc-format-selected)
```

需要做修改的是，在 :CocConfig 中加上配置, 设置根据 esline 规则进行格式化
```
{
 "prettier.eslintIntegration": true
}
```

## coc-snippets


```
" 使用 <C-l> 觸發程式碼片段擴展
imap <C-l> <Plug>(coc-snippets-expand)
" 使用 <C-j> 用於程式碼片段的可視佔位符的選擇文本
vmap <C-j> <Plug>(coc-snippets-select)

" 使用 <C-j> 跳轉到下一個佔位符，coc.nvim 預設值
let g:coc_snippet_next = '<c-j>'
" 使用 <C-k> 要跳轉到上一個佔位符，coc.nvim 預設值
let g:coc_snippet_prev = '<c-k>'
```

