``` js
{
  // ESLint 插件配置
  "editor.codeActionsOnSave": {
    "source.fixAll": true
  },
  //导入文件时是否携带文件的扩展名
  "path-autocomplete.extensionOnImport": true,
  //配置 @ 的路径提示
  "path-autocomplete.pathMappings": {
    "@": "${folder}/src"
  },
  "explorer.confirmDelete": false,
  "editor.wordWrap": "on",
  "workbench.editor.enablePreview": false,
  "cssrem.rootFontSize": 75,
  "workbench.colorTheme": "Monokai Dimmed",
  "files.autoSave": "onFocusChange",
  "files.associations": {
    "*.art": "html"
  },
  "editor.tabSize": 2,
  "editor.formatOnSave": true,
// prettier 配置路径
"prettier.configPath": "C:\\Users\\鲸落\\.prettier",
"eslint.alwaysShowStatus": true,
"prettier.trailingComma": "none",
"prettier.semi": false,
// 每行文字个数超出此限制将会被迫换行
"prettier.printWidth": 300,
// 使用单引号替换双引号
"prettier.singleQuote": true,
"prettier.arrowParens": "avoid",
// 设置 .vue 文件中，HTML代码的格式化插件
"vetur.format.defaultFormatter.html": "js-beautify-html",
"vetur.ignoreProjectWarning": true,
"vetur.format.defaultFormatterOptions": {
    "js-beautify-html": {
        "wrap_attributes": false
    },
    "prettier": {
        "printWidth": 300,
        "trailingComma":"none",
        "semi": false,
        "singleQuote": true,
        "arrowParens": "avoid"
    }
},
"editor.insertSpaces": false,
"[javascript]": {
	"editor.defaultFormatter": "esbenp.prettier-vscode"
},
"[vue]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
}
```

