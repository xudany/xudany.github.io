---
title: CodeMirror实现自定义提示功能
date: 2020-07-21
categories:
- CodeMirror
tags:
- react
- Feature
- next.js
---

ლ(′◉❥◉｀ლ)嘛，反正就是需求要做一个简单的SQL编辑器~

选了CodeMirror，好的，开始。

## 需求

- 执行选中部分SQL
- 输入自动提示SQL代码功能
- 邻近字段自动提示功能
- 在适当位置输入的时候自动提示表名、字段名等



## 安装

项目用的是react，所以这里引入了 [react-codemirror2](https://github.com/scniro/react-codemirror2)

```bash
npm install react-codemirror2 codemirror --save
```



## 引入

```js
// react-codemirror2 有 UnControlled 和 Controlled 两种组件，区别可看官网
import {UnControlled as CodeMirror} from 'react-codemirror2'
// 或者
import {Controlled as CodeMirror} from 'react-codemirror2'
```

因为项目还用的是next，所以在项目中，我需要动态引入，不然会报错

```js
import dynamic from 'next/dynamic';
const CodeMirror = dynamic(() => import('react-codemirror2').then((mod) => mod.UnControlled), {
  ssr: false,
});
```

然后这里有一个要注意的点，CodeMirror的主题样式和插件是需要手动引入依赖的，我选的是neo这个主题，所以我除了引入CodeMirror基本样式外还需要引入这个主题的css样式

```js
import 'codemirror/lib/codemirror.css';
import 'codemirror/theme/neo.css';
```

然后CodeMirror的提示插件也需要手动引入：

```js
  require('codemirror/addon/hint/show-hint.js');
```

SQL提示功能，邻近字段提示功能，占位符等功能，总之在官网 [Addons](https://codemirror.net/doc/manual.html#addons) 目录下的都需要手动引入，最终我的引入代码是

```js
import 'codemirror/lib/codemirror.css';
import 'codemirror/theme/neo.css';
import 'codemirror/addon/hint/show-hint.css';
import dynamic from 'next/dynamic';
const CodeMirror = dynamic(() => import('react-codemirror2').then((mod) => mod.UnControlled), {
  ssr: false,
});

// next.js的特殊引入方式
try {
  require('codemirror/mode/sql/sql');
  require('codemirror/addon/hint/sql-hint.js');
  require('codemirror/addon/hint/show-hint.js');
  require('codemirror/addon/display/placeholder.js');
  require('codemirror/addon/edit/closebrackets.js');
  require('codemirror/addon/hint/anyword-hint');
} catch (e) {
  console.error(e);
}
```



## 实现

首先官网是有提供一个简单SQL的提示插件的，所以基础的SQL代码提示和邻近字段提示只要配置就可以了。

```js
      <CodeMirror
        className="os-code-mirror"
        options={{
          // 编辑器模式
          mode: { name: 'text/x-mysql' },
          // 提示触发快捷键配置
          extraKeys: { Ctrl: 'autocomplete' },
          // 编辑器主题
          theme: 'neo',
          // 编辑器的左侧显示行号
          lineNumbers: true,
          // 占位符
          placeholder: '请输入sql语句',
          // 自动关闭方括号和引号
          autoCloseBrackets: true,
          // 提示配置
          hintOptions: {
            // 自动匹配唯一值
            completeSingle: false,
          },
          // 是否换行
          lineWrapping: true,
        }}
      />
```

所以上面这段代码已经能够满足按`Ctrl`键提示SQL代码和邻近字段的功能，但是需求是要自动提示，不需要按任何键，所以要改，要实现自定义提示的触发时间，方法 `execCommand('autocomplete')` 就是用来触发提示的，所以修改后的代码

```js
      <CodeMirror
        className="os-code-mirror"
        options={{
          // 编辑器模式
          mode: { name: 'text/x-mysql' },
          // 编辑器主题
          theme: 'neo',
          // 编辑器的左侧显示行号
          lineNumbers: true,
          // 占位符
          placeholder: '请输入sql语句',
          // 自动关闭方括号和引号
          autoCloseBrackets: true,
          },
          // 是否换行
          lineWrapping: true,
        }}
        onChange={(editor, changeObj, value) => {
          if (changeObj.origin === '+input' && changeObj.text[0] !== ' ' && changeObj.text[0] !== ';') {
            editor.execCommand('autocomplete');
          }
        }}
      />
```

然后就加上获取到选中部分内容，用来实现执行部分内容

```js
      <CodeMirror
        className="os-code-mirror"
        options={{
          // 编辑器模式
          mode: { name: 'text/x-mysql' },
          // 编辑器主题
          theme: 'neo',
          // 编辑器的左侧显示行号
          lineNumbers: true,
          // 占位符
          placeholder: '请输入sql语句',
          // 自动关闭方括号和引号
          autoCloseBrackets: true,
          },
          // 是否换行
          lineWrapping: true,
        }}
        // 获取到选中部分内容，用来实现执行部分内容
        onCursorActivity={(cm) => {
          onCursor(cm);
        }}
        onChange={(editor, changeObj, value) => {
          if (changeObj.origin === '+input' && changeObj.text[0] !== ' ' && changeObj.text[0] !== ';') {
            editor.execCommand('autocomplete');
          }
        }}
      />
```

啊_(:з」∠)_，好麻烦啊，这样一步步加功能和代码，算了算，直接贴最终的代码吧哈哈哈哈哈哈（是的，我懒了）,总之解决了各种奇奇怪骨气的坑，还有中间实在没思路就去看SQL插件的源码，看看人家怎么写的，改一改啊哈哈哈哈哈~

最终代码调整： 

```js
  let preContent: any;

  // 是否触发搜索条件
  const controlSearch = (editor: any, changeObj: any) => {
    // 获取当前光标相关数据
    const cur = editor.getCursor();
    // 获取当前行数据
    const curLine = editor.getLine(cur.line);

    const sentence = curLine.slice(0, cur.ch).split(' ');

    // 获取当前光标之前的字段
    const upperSen = _.toUpper(sentence[sentence.length - 2]);

    if (changeObj.origin !== 'paste' && (upperSen === 'FROM' || upperSen === 'JOIN')) {
      setState((pre) => {
        if (changeObj.origin === 'complete') {
          pre.isSearch = false;
          pre.preContent = pre.content;
          pre.content = null;
        } else {
          pre.isSearch = true;
          pre.preContent = pre.content;
          pre.content = sentence[sentence.length - 1];
        }
      });
      editor.execCommand('autocomplete');
    } else {
      setState((pre) => {
        pre.isSearch = false;
        pre.preContent = pre.content;
        pre.content = null;
      });

      if (
        changeObj.origin === '+input' &&
        changeObj.text[0] !== ' ' &&
        changeObj.text[0] !== ';' &&
        curLine !== ''
      ) {
        editor.execCommand('autocomplete');
      }
    }
  };

  const onCursor = _.throttle(
    (cm) => {
      if (getSelection) {
        getSelection(cm, cm.getSelection());
      }
    },
    500,
    { leading: false, trailing: true }
  );

  const onChangeEvent = _.throttle(
    (editor, data, value) => {
      if (getChangeValue) {
        getChangeValue(editor, data, value);
      }
      controlSearch(editor, data);
    },
    500,
    { leading: false, trailing: true }
  );

  const onInputReadEvent = _.throttle(
    (editor, changeObj) => {
      if (onInputReadEvent) {
        onInputReadEvent(editor, changeObj);
      }
    },
    1000,
    {
      leading: false,
      trailing: true,
    }
  );
```




---

```js
      <CodeMirror
        className="os-code-mirror"
        options={{
          // 编辑器模式
          mode: { name: 'text/x-mysql' },
          // 编辑器主题
          theme: 'neo',
          // 编辑器的左侧显示行号
          lineNumbers: true,
          // 占位符
          placeholder: '请输入sql语句',
          // 自动关闭方括号和引号
          autoCloseBrackets: true,
          // 提示配置
          hintOptions: {
            // 自动匹配唯一值
            completeSingle: false,
            hint: async (editor, mode) => {
              let custom;

              if (
                state.isSearch &&
                autoComplete &&
                state.preContent !== state.content &&
                preContent !== state.content
              ) {
                setState((pre) => {
                  pre.preContent = pre.content;
                });
                preContent = state.content;
                custom = await autoComplete(state.content);
              }

              const anyHint = instance.hint.anyword(editor, mode);
              const sqlHint = instance.hint.sql(editor, mode);

              const words = new Set([
                ...(custom || []),
                ...(extraHint || []),
                ...anyHint.list,
                ...sqlHint.list,
              ]);
              return {
                list: Array.from(words),
                from: sqlHint.from,
                to: sqlHint.to,
              };
            },
          },
          // 是否换行
          lineWrapping: true,
        }}
        // 获取到选中部分内容，用来实现执行部分内容
        onCursorActivity={(cm) => {
          onCursor(cm);
        }}
        // 内容改变时候触发
        onChange={(editor, data, value) => {
          onChangeEvent(editor, data, value);
        }}
        // 赋予实例（由于 next.js 的特殊性所以需要等待编辑器渲染完之后才可以）
        editorDidMount={() => {
          instance = require('codemirror');
        }}
      />
```




## 参考资料

[CodeMirror](https://codemirror.net/index.html)

[使用 CodeMirror 打造属于自己的在线代码编辑器](https://zhuanlan.zhihu.com/p/32126618)

[sql-hint](https://codemirror.net/addon/hint/sql-hint.js)

[anyword-hint](https://codemirror.net/addon/hint/anyword-hint.js)

[codemirror实现SQL代码自动提示功能](https://blog.csdn.net/Melody_Susan/article/details/93020189)

[React中使用CodeMirror实现SQL编辑器基本功能](https://juejin.im/post/6844904081517985799)

