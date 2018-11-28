---
title: react-native 通讯录字母跳转分组功能
date: 2018-11-14
categories:
- react-native
tags:
- react-native
- component
- FlatList
- SectionList
---
# react-native 通讯录字母跳转分组功能

## 设计图

使用 react-native 做一个如下图所示的通讯录

![10_40_41__11_28_2018](/Users/dany/temp/10_40_41__11_28_2018.jpg)



## 功能需求

1. 汉字转拼音功能
2. 需要对通讯录人员的数据按名字拼音第一个字母进行分组
3. 右侧字母是根据通讯人员的数据动态生成的
4. 右侧字母需要有跳转功能
5. 需要获取通讯录人员名称的第一个字



## 实现分析

1. 汉字转拼音使用第三方插件 [pinyin](https://github.com/hotoo/pinyin)
2. 实现分组的效果可以使用 react-native 高性能的分组列表组件 `SectionList`
3. 右侧字母表使用 react-native 简单列表组件 `FlatList` 就可以了
4. 其他功能做数据处理就好



## 代码实现

### 通讯录人员数据

```javascript
let testData = [
  {name: '盖伦'},
  {name: '崔丝塔娜'},
  {name: "大发明家"},
  {name: '武器大师'},
  {name: "武器大师"},
  {name: '刀妹'},
  {name: "卡特琳娜"},
  {name: '盲僧'},
  {name: "蕾欧娜"},
  {name: "拉克丝"},
  {name: "剑圣"},
  {name: "赏金"},
  {name: "发条"},
  {name: "瑞雯"},
  {name: "提莫"},
  {name: "卡牌"},
  {name: "剑豪"},
  {name: "琴女"},
  {name: "木木"},
  {name: "雪人"},
  {name: "安妮"},
  {name: "薇恩"},
  {name: "小法师"},
  {name: "艾尼维亚"},
  {name: "奥瑞利安索尔"},
  {name: "布兰德"},
  {name: "凯特琳"},
  {name: "虚空"},
  {name: "机器人"},
  {name: "挖掘机"},
  {name: "EZ"},
  {name: "暴走萝莉"},
  {name: "艾克"},
  {name: "波比"},
  {name: "赵信"},
  {name: "牛头"},
  {name: "九尾"},
  {name: "菲兹"},
  {name: "寒冰"},
  {name: "猴子"},
  {name: "深渊"},
  {name: "凯南"},
  {name: "诺克萨斯"},
  {name: "祖安"},
  {name: "德莱文"},
  {name: "德玛西亚王子"},
  {name: "豹女"},
  {name: "皮城执法官"},
  {name: "泽拉斯"},
  {name: "岩雀"},
]
```

### 原始数据处理

获取原始数据

```javascript
let list = testData;
let sections = [], letterArr = [];
```

右侧字母栏数据处理

```javascript
list.map((item, index) => {

  letterArr.push(pinyin(item.name.substring(0, 1), {
    style: pinyin.STYLE_FIRST_LETTER,
  })[0][0].toUpperCase());

  letterArr = [...new Set(letterArr)].sort();

  this.setState({letterArr: letterArr})
});
```

分组数据处理

```javascript
letterArr.map((item, index) => {
  sections.push({
    title: item,
    data: []
  })
});

list.map(item => {
  let listItem = item;
  sections.map(item => {
    let first = listItem.name.substring(0, 1);
    let test = pinyin(first, {style: pinyin.STYLE_FIRST_LETTER})[0][0].toUpperCase();

    if (item.title == test) {
      item.data.push({firstName: first, name: listItem.name});
    }
  })
});

this.setState({sections: sections})
```

### 组件代码

```javascript
<Content contentContainerStyle={Style.contentStyle}>

  <SectionList
    ref="_sectionList"
    renderItem={({item, index}) => this._renderItem(item, index)}
    renderSectionHeader={this._renderSectionHeader}
    sections={sections}
    keyExtractor={(item, index) => item + index}
    ItemSeparatorComponent={() => <View
      style={{height: scaleSize(1), backgroundColor: '#E3E3E3', marginHorizontal: scaleSize(10)}}/>}
  />

  {/*右侧字母栏*/}
  <View style={{position: 'absolute', width: scaleSize(26), right: 0, top: scaleSize(top_offset)}}>
    <FlatList
      data={letterArr}
      keyExtractor={(item, index) => index.toString()}       
      renderItem={({item, index}) =>
        <TouchableOpacity
          style={Style.letterStyle}
          onPress={() => {
            this._onSectionselect(index)
          }}
        >
          <Text style={{fontSize: setSpText(11), color: Colors.StatusHover}}>{item.toUpperCase()}</Text>
        </TouchableOpacity>
      }
    />
  </View>

</Content>
```

### 分组列表的renderItem

```javascript
_renderItem(item, index) {
  const {showIndex} = this.state;
  return (
    <TouchableOpacity
      style={
        Style.sectionItem
      }
      activeOpacity={.75}
      onPress={() => {
        this.setState({
          showIndex: item.name,
        });
      }}
    >
      <View style={Style.sectionItemText}>
        <View style={Style.firstName}>
          <Text style={Style.firstNameText}>{item.firstName}</Text>
        </View>
        <View>
          <Text style={Style.nameStyle}>{item.name}</Text>
        </View>
      </View>
    </TouchableOpacity>
  );
}
```

### 分组列表的头部

```javascript
_renderSectionHeader(sectionItem) {
  const {section} = sectionItem;
  return (
    <View style={Style.sectionHeader}>
      <Text style={{fontSize: setSpText(14), color: '#657294'}}>{section.title.toUpperCase()}</Text>
    </View>
  );
}
```

### 字母关联分组跳转

```javascript
_onSectionselect = (key) => {

  this.refs._sectionList.scrollToLocation({
    itemIndex: 0,
    sectionIndex: key,
    viewOffset: 20,
  })

};
```

### 完整代码

```
import React, {Component} from "react";
import {
  View,
  Text,
  Dimensions,
  SectionList,
  FlatList,
  TouchableOpacity,
} from 'react-native'
import {
  Container,
  Content,
  StyleProvider
} from 'native-base';
import getTheme from '../../../../common/native-base-theme/components';
import platform from '../../../../common/native-base-theme/variables/platform';
import {setSpText, Colors, scaleSize} from '../../../../common/index.js';
import CommonHeader from "../../../../components/CommonHeader";
import Style from './style.js';
import pinyin from 'pinyin';


let testData = [
  {name: '盖伦'},
  {name: '崔丝塔娜'},
  {name: "大发明家"},
  {name: '武器大师'},
  {name: "武器大师"},
  {name: '刀妹'},
  {name: "卡特琳娜"},
  {name: '盲僧'},
  {name: "蕾欧娜"},
  {name: "拉克丝"},
  {name: "剑圣"},
  {name: "赏金"},
  {name: "发条"},
  {name: "瑞雯"},
  {name: "提莫"},
  {name: "卡牌"},
  {name: "剑豪"},
  {name: "琴女"},
  {name: "木木"},
  {name: "雪人"},
  {name: "安妮"},
  {name: "薇恩"},
  {name: "小法师"},
  {name: "艾尼维亚"},
  {name: "奥瑞利安索尔"},
  {name: "布兰德"},
  {name: "凯特琳"},
  {name: "虚空"},
  {name: "机器人"},
  {name: "挖掘机"},
  {name: "EZ"},
  {name: "暴走萝莉"},
  {name: "艾克"},
  {name: "波比"},
  {name: "赵信"},
  {name: "牛头"},
  {name: "九尾"},
  {name: "菲兹"},
  {name: "寒冰"},
  {name: "猴子"},
  {name: "深渊"},
  {name: "凯南"},
  {name: "诺克萨斯"},
  {name: "祖安"},
  {name: "德莱文"},
  {name: "德玛西亚王子"},
  {name: "豹女"},
  {name: "皮城执法官"},
  {name: "泽拉斯"},
  {name: "岩雀"},
]


export default class ContactsListScreen extends Component {

  constructor() {
    super();
    this.state = {
      sections: [],       //section数组
      letterArr: ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z'],      //首字母数组
      showIndex: -1
    };
  }


  componentWillMount() {


    // 暂时静态数据替换
    //获取联系人列表
    let list = testData;
    let sections = [], letterArr = [];

    // 右侧字母栏数据处理
    list.map((item, index) => {

      letterArr.push(pinyin(item.name.substring(0, 1), {
        style: pinyin.STYLE_FIRST_LETTER,
      })[0][0].toUpperCase());

      letterArr = [...new Set(letterArr)].sort();

      this.setState({letterArr: letterArr})
    });

    // 分组数据处理
    letterArr.map((item, index) => {
      sections.push({
        title: item,
        data: []
      })
    });

    list.map(item => {
      let listItem = item;
      sections.map(item => {
        let first = listItem.name.substring(0, 1);
        let test = pinyin(first, {style: pinyin.STYLE_FIRST_LETTER})[0][0].toUpperCase();

        if (item.title == test) {
          item.data.push({firstName: first, name: listItem.name});
        }
      })
    });

    this.setState({sections: sections})

  }

  // 字母关联分组跳转
  _onSectionselect = (key) => {

    this.refs._sectionList.scrollToLocation({
      itemIndex: 0,
      sectionIndex: key,
      viewOffset: 20,
    })

  };

  // 分组列表的头部
  _renderSectionHeader(sectionItem) {
    const {section} = sectionItem;
    return (
      <View style={Style.sectionHeader}>
        <Text style={{fontSize: setSpText(14), color: '#657294'}}>{section.title.toUpperCase()}</Text>
      </View>
    );
  }

  // 分组列表的renderItem
  _renderItem(item, index) {
    const {showIndex} = this.state;
    return (
      <TouchableOpacity
        style={
          Style.sectionItem
        }
        activeOpacity={.75}
        onPress={() => {
          this.setState({
            showIndex: item.name,
          });
        }}
      >
        <View style={Style.sectionItemText}>
          <View style={Style.firstName}>
            <Text style={Style.firstNameText}>{item.firstName}</Text>
          </View>
          <View>
            <Text style={Style.nameStyle}>{item.name}</Text>
          </View>
        </View>
      </TouchableOpacity>
    );
  }

  render() {
    const {letterArr, sections} = this.state;
    // 偏移量
    const top_offset = (Dimensions.get('window').height - letterArr.length * scaleSize(16)) / 2;

    return (
      <StyleProvider style={getTheme(platform)}>
        <Container>
          <CommonHeader title={'选择执行人员'} leftOnPress={() => {
            this.props.navigation.goBack()
          }}/>
          <Content contentContainerStyle={Style.contentStyle}>

            <SectionList
              ref="_sectionList"
              renderItem={({item, index}) => this._renderItem(item, index)}
              renderSectionHeader={this._renderSectionHeader}
              sections={sections}
              keyExtractor={(item, index) => item + index}
              ItemSeparatorComponent={() => <View
                style={{height: scaleSize(1), backgroundColor: '#E3E3E3', marginHorizontal: scaleSize(10)}}/>}
            />

            {/*右侧字母栏*/}
            <View style={{position: 'absolute', width: scaleSize(26), right: 0, top: scaleSize(top_offset)}}>
              <FlatList
                data={letterArr}
                keyExtractor={(item, index) => index.toString()}      
                renderItem={({item, index}) =>
                  <TouchableOpacity
                    style={Style.letterStyle}
                    onPress={() => {
                      this._onSectionselect(index)
                    }}
                  >
                    <Text style={{fontSize: setSpText(11), color: Colors.StatusHover}}>{item.toUpperCase()}</Text>
                  </TouchableOpacity>
                }
              />
            </View>

          </Content>
        </Container>
      </StyleProvider>
    )
  }
}
```



## 效果图

![15_01_58__11_28_2018](/Users/dany/temp/15_01_58__11_28_2018.jpg)

## 参考文章

[react-native使用SectionList做通讯录列表，分组跳转](https://blog.csdn.net/halo1416/article/details/82148873?utm_source=blogxgwz0)

十分感谢~