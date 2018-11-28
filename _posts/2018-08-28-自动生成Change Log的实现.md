---
title: 自动生成Change Log的实现
date: 2018-08-28
categories:
- Change Log 
tags:
- Change Log
- commander 
- inquirer 
- node
---

# 自动生成Change Log的实现

### 开发目的

快速可交互的搭建生成Change Log更新日志，来提高自己的工作效率。



### 实现思路

要实现Change log自动生成，需要有脚本可以根据项目提交的commit自动生成Change log ，然后还有脚本自动生成版本号，而且每次生成可以自动修改版本号，还需要把生成的Change log 的markdown文件转换成HTML，以便我们在项目中使用 ，既然要转成HTML文件，那就还需要有HTML的模板。



### 第三方库

搭建changeLog过程中使用到的库

- conventional-changelog    ——根据commit生成Change log
- conventional-changelog-cli   ——根据commit生成Change log的脚手架
- marked.js  ——解析markdown文件，markdown文件转换成HTML
- commander  ——可以自动的解析命令和参数，用于处理用户输入的命令
- chalk  ——在终端给输出的命令行字体加上颜色
- log-symbols  ——在终端可以输出 √ 或 × 等的图标
- inquirer  ——通用的命令行用户界面集合，用于和用户进行交互
- shelljs  ——可使用shell命令来操作文件



### 具体实现

根据实现思路，分步骤实现此功能

1. 脚本自动生成版本号，而且每次生成可以自动修改版本号

    ```
    // 读取版本号
    function readFile(url) {
       var promise = new Promise((resolve, reject) => {
          fs.readFile(path.resolve(__dirname, url), 'utf8', (err, rs) => {
             if (err) {
                reject(chalk.red.bold(logSymbols.error, '读取文件失败 \n' + err));
             } else {
                let temp = JSON.parse(rs);
                resolve(temp);
             }
          })
       })
       return promise
    }

    // 写入版本号
    function writeFile(url, version) {

        var promise = new Promise((resolve, reject) => {
            fs.readFile(path.resolve(__dirname, url), 'utf8', (err, data) => {
                if (err) {
                    throw err;
                    reject(chalk.red.bold(logSymbols.error, '读取文件失败 \n' + err))
                } else {
                    var tempData = JSON.parse(data);
                    tempData.version = version;
                    var content = JSON.stringify(tempData, null, 4)
                    fs.writeFile(path.resolve(__dirname, url), content, err => {
                        if (err) {
                            reject(chalk.red.bold(logSymbols.error, '写入文件失败 \n' + err))
                        } else {
                            console.log(chalk.green.bold(logSymbols.success, url + ' 写入版本号成功'));
                            resolve(true)
                        }
                    })
                }
            })
        })
        return promise
    }

    // 比较版本号
    function compareVersion(v1, v2) {
        var arr1 = v1.replace(/[-_]/g, '.').split('.');
        var arr2 = v2.replace(/[-_]/g, '.').split('.');
        var len = Math.max(arr1.length, arr2.length);
        for (var i = 0; i < len; i++) {
            if (parseInt(arr1[i]) == parseInt(arr2[i])) continue;
            return parseInt(arr1[i]) < parseInt(arr2[i]) ? true : false;
        }
        return false;
    }

    ```



2. 把生成的Change log 的markdown文件转换成HTML

   ```
   // 读取html模板文件
   fs.readFile(path.resolve(__dirname, '../static/changeLog/template.html'), 'utf8', (err, template) => {
      if (err) {
         throw err;
      } else {
         fs.readFile(filePath, 'utf8', (err, markContent) => {
            
            let replaceList = [
               {
                  're': /http:\/\/git\.ascs\.tech\/website/g,
                  'reAim': 'https://bitbucket.ascs.tech/projects/WEBSITE/repos'
               },
            ];
            
            for (let item of replaceList) {
               markContent = markContent.replace(item.re, item.reAim);
            }
            
            if (err) {
               throw err;
            } else {
               let htmlStr = marked(markContent.toString());
               template = template.replace('@markdown', htmlStr);
               
               fs.writeFile(path.resolve(__dirname, '../static/changeLog/CHANGELOG.html'), template, err => {
                  if (err) {
                     throw err;
                  } else {
                     console.log(chalk.green.bold(logSymbols.success, ' 成功将markdown文件转换成HTML文件'));
                  }
               })
            }
         })
      }
   })
   ```



3. HTML的模板

   ```
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>ChangeLog</title>
       <link href='./css/changeLog.css' rel='stylesheet' type='text/css'/>
   </head>
   
   <body>
       <div id="head">
           <header>
               <img class="logoImg" src="C:/Users/Administrator/Desktop/images/logo.png">
               <span id="headerTitle">CityWorks® Intranet<span>
                   <img class="toolsImg" src="C:/Users/Administrator/Desktop/images/archive.png">
                   <img class="toolsImg" src="C:/Users/Administrator/Desktop/images/new.png">
                   <img class="toolsImg" src="C:/Users/Administrator/Desktop/images/search.png">
               </header>
           </div>
           <div id="logTiile">
               <span id="spanTiile"><img src="C:/Users/Administrator/Desktop/images/title.png">更新日志</span>
               <hr>
           </div>
           <div id='write'>
               @markdown
           </div>
       </body>
       <script>
           (function changeStyle(){
             let h2s = document.querySelectorAll("h2");
             let h2sArr = [...h2s];
             h2sArr.forEach(h2=>{
                let content = h2.innerHTML;
                let newContentArr = content.split(" ");
                let version = newContentArr[0];
                let date = newContentArr[1].replace(/[\(,\)]/g,"");
                h2.innerHTML = `${version}<span class='version-date'>${date}</span>`;
            })
         })()
     </script>
     </html>
   ```



4. 使用command.js来解析命令和参数

   ```
   program
      .command('simple')
      .option('-s --simple')
      .alias('s')
      .description('需要配置文件的简单初始化')
      .action(function () {
   ```



5. 使用inquirer来进行命令行的交互

    ```
    inquirer
       .prompt([
          {
             type: 'input',
             name: 'version',
             message: '请输入新的版本号：',
             validate: function (input) {
                var isVersion = changeVersion.compareVersion(rs.version, input);
                if (isVersion) {
                   return true
                } else {
                   return '请输入正确的版本号'
                }
             }
          }
       ])
       .then((answers) => {
          (async function test() {
             await changeVersion.writeHtmlFile(answers.version)
             for (let i of config.changeVersionPath) {
                await changeVersion.writeFile(i, answers.version)
             }
             await shell.exec('conventional-changelog -p angular -i CHANGELOG.md -s')
             await console.log(chalk.green.bold(logSymbols.success, ' 成功增加版本信息'))
             var execUrl = "./bin/createChangeLog.js";
             await shell.exec('node ' + execUrl);
          })()
       });
    ```
