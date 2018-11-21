---
title: react-native 常用组件之TextInput
date: 2018-10-20
categories:
- react-native
tags:
- react-native
- component
---

# react-native 常用组件之TextInput

TextInput是一个允许用户在应用中通过键盘输入文本的基本组件。本组件的属性提供了多种特性的配置，譬如自动完成、自动大小写、占位文字，以及多种不同的键盘类型（如纯数字键盘）等等。

## TextInput基本使用方法

```javascript
export default class UselessTextInput extends Component {
  constructor(props) {
    super(props);
    this.state = { text: 'Useless Placeholder' };
  }

  render() {
    return (
      <TextInput
        style={{height: 40, borderColor: 'gray', borderWidth: 1}}
        onChangeText={(text) => this.setState({text})}
        value={this.state.text}
      />
    );
  }
}
```

### TextInput存值

通过`onChangeText`事件的触发，用`setState`把用户的输入写入state中

### TextInput取值

在需要取值的地方从`this.state`中取出值



## Android上需要注意的坑

1. `TextInput`在Android上默认有一个底边框，同时会有一些padding。如果要想使其看起来和iOS上尽量一致，则需要设置`padding: 0`，同时设置`underlineColorAndroid="transparent"`来去掉底边框。
2. 在Android上如果设置`multiline = {true}`，文本默认会垂直居中，可设置`textAlignVertical: 'top'`样式来使其居顶显示。
3. 在Android上长按选择文本会导致`windowSoftInputMode`设置变为`adjustResize`，这样可能导致绝对定位的元素被键盘给顶起来。要解决这一问题需要在AndroidManifest.xml中明确指定合适的`windowSoftInputMode`( <https://developer.android.com/guide/topics/manifest/activity-element.html> )值，或是自己监听事件来处理布局变化。



## TextInput的属性

### iOS以及Android通用属性：

#### `allowFontScaling`

控制字体是否要根据系统的“字体大小”辅助选项来进行缩放。默认值为`true`。

#### `autoCapitalize`

控制TextInput是否要自动将特定字符切换为大写，一些键盘类型不支持，比如 `name-phone-pad`

- `characters`: 所有的字符。
- `words`: 每个单词的第一个字符。
- `sentences`: 每句话的第一个字符（默认）。
- `none`: 不切换。

#### `autoCorrect`

是否开启拼写自动修正。默认值是true。

#### `autoFocus`

如果为true，在`componentDidMount`后会获得焦点。默认值为false。自动触发keyboard。

ps：在iOS模拟器中，键盘不会出现，需要按`command` + k 调出键盘

#### `blurOnSubmit`

如果为true，文本框会在提交的时候失焦。对于单行输入框默认值为true，多行则为false。

注意：对于多行输入框来说，如果将`blurOnSubmit`设为true，则在按下回车键时就会失去焦点同时触发`onSubmitEditing`事件，而不会换行。

#### `caretHidden`

是否隐藏光标。默认值为false。

#### `contextMenuHidden`

是否隐藏上下文菜单，由于长按`TextInput` 后会出现在一个编辑菜单，比如复制、粘贴、剪切、全选等选项。`contextMenuHidden`属性则可以禁止此菜单的出现。默认值为 `false`。

#### `defaultValue`

提供一个文本框中的初始值。当用户开始输入的时候，值就可以改变。在一些简单的使用情形下，如果不想用监听消息然后更新value属性的方法来保持属性和状态同步的时候，就可以用defaultValue来代替。

#### `editable`

如果为false，文本框是不可编辑的。默认值为true。

#### `keyboardType`

决定弹出的何种软键盘的，譬如`numeric`（纯数字键盘）。

这些值在所有平台都可用：

- `default`
- `number-pad`
- `decimal-pad`
- `numeric`
- `email-address`
- `phone-pad`

下面的值仅iOS可用：

- `ascii-capable`
- `numbers-and-punctuation`
- `url`
- `name-phone-pad`
- `twitter`
- `web-search`

下面的值仅Android可用：

- `visible-password`



#### `maxLength`

  限制文本框中最多的字符数。使用这个属性而不用JS逻辑去实现，可以避免闪烁的现象。

#### `multiline`

如果为true，文本框中可以输入多行文字。默认值为false。注意安卓上如果设置`multiline = {true}`，文本默认会垂直居中，可设置`textAlignVertical: 'top'`样式来使其居顶显示。

#### `onBlur`

当文本框失去焦点的时候调用此回调函数。

#### `onChange`

当文本框内容变化时调用此回调函数。

#### `onChangeText`

当文本框内容变化时调用此回调函数。改变后的文字内容会作为参数传递。

#### `onContentSizeChange`

当文本框的高度发生变化时回调该函数。传递给回调函数的参数为{ nativeEvent: { contentSize: { width, height } } }`，当TextInput的高度可变时，调用onLayout方法并将宽高传递给它，并重新绘制该组件。例如：

```javascript
  onContentSizeChange = (event) => {
      this.setState({
          height: event.nativeEvent.contentSize.height
      });
  }
```

只有在多行文本输入是才可用。

#### `onEndEditing`

当文本输入结束后调用此回调函数。

#### `onFocus`

当文本框获得焦点的时候调用此回调函数。

#### `onKeyPress`

当一个键被按下的时候调用此回调。传递给回调函数的参数为`{ nativeEvent: { key: keyValue } }`，其中`keyValue`即为被按下的键。会在onChange之前调用。注意：在Android上只有软键盘会触发此事件，物理键盘不会触发。

#### `onLayout`

当组件加载或者布局变化的时候调用，参数为`{x, y, width, height}`。

#### `onScroll`

在内容滚动时持续调用，传回参数的格式形如`{ nativeEvent: { contentOffset: { x, y } } }`。也可能包含其他和滚动事件相关的参数，但是在Android上，出于性能考虑，不会提供`contentSize`参数。

#### `onSelectionChange`

长按选择文本时，选择范围变化时调用此函数，传回参数的格式形如`{ nativeEvent: { selection: { start, end } } }`。

#### `onSubmitEditing`

此回调函数当软键盘的`确定`/`提交`按钮被按下的时候调用此函数。如果`multiline={true}`，此属性不可用。

#### `placeholder`

如果没有任何文字输入，会显示此字符串。

#### `placeholderTextColor`

占位字符串显示的文字颜色。

#### `returnKeyType`

决定“确定”按钮显示的内容。在Android上你还可以使用`returnKeyLabel`。

下列这些选项是跨平台可用的：

- `done`
- `go`
- `next`
- `search`
- `send`

下列这些选项仅Android可用：

- `none`
- `previous`

下列这些选项仅iOS可用：

- `default`
- `emergency-call`
- `google`
- `join`
- `route`
- `yahoo`

#### `secureTextEntry`

如果为true，文本框会遮住之前输入的文字，这样类似密码之类的敏感文字可以更加安全。默认值为false。`multiline={true}`时不可用。

#### `selection`

设置选中文字的范围（指定首尾的索引值）。如果首尾为同一索引位置，则相当于指定光标的位置。

#### `selectionColor`

设置输入框高亮时的颜色（还包括光标）。

#### `selectTextOnFocus`

如果为true，当获得焦点的时候，所有的文字都会被选中。

#### `style`

注意，并非所有的文本样式都能支持，不支持的不完整列表包括:

- `borderLeftWidth`
- `borderTopWidth`
- `borderRightWidth`
- `borderBottomWidth`
- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomRightRadius`
- `borderBottomLeftRadius`

更多相关内容移步 [Issue#7070](https://github.com/facebook/react-native/issues/7070)

#### `value`

文本框中的文字内容。 TextInput是一个受约束的(Controlled)的组件，意味着如果提供了value属性，原生值会被强制与value属性保持一致。有些情况下会导致一些闪烁现象——一个常见的原因就是通过不改变value来阻止用户进行编辑。如果希望阻止用户输入，可以考虑设置`editable={false}`；如果是希望限制输入的长度，可以考虑设置`maxLength`属性，这两个属性都不会导致闪烁。



### 只支持iOS的属性：

#### `clearButtonMode`

是否要在文本框右侧显示“清除”按钮。仅在单行模式下可用。默认值为`never`。只支持iOS平台。

- `nerver`: 永远不显示“清除”按钮
- `while-editing`: 在编辑的过程中显示“清除”按钮
- `unless-editing`: 在编辑的过程中不显示“清除”按钮，其他情况下显示
- `always`: 一直显示“清除”按钮

#### `clearTextOnFocus`

是否每次开始输入的时候清除文本框的内容。默认值为false，只支持iOS平台。

#### `dataDetectorTypes`

设置 text input 内能被转化为可点击URL的数据的类型。当且仅当`multiline={true}`和`editable={false}`时起作用。默认情况下不检测任何数据类型。

可接受一个类型值或类型值数组。只支持iOS。

`dataDetectorTypes`的可用值有:

- `'phoneNumber'`: 电话号码

- `'link'`:  链接

- `'address'`: 地址

- `'calendarEvent'`: 日历事件

- `'none'`: 无

- `'all'`: 全部，所有



#### `enablesReturnKeyAutomatically`

  如果为true，键盘会在文本框内没有文字的时候禁用确认按钮。默认值为false。只支持iOS。



#### `keyboardAppearance`

指定键盘的颜色。可选参数：'default', 'light', 'dark'。只支持iOS。



#### `scrollEnabled`

如果为false，将禁用文本视图的滚动。默认值为true。只适用于multiline={true}。只支持iOS。

#### `selectionState`

 `DocumentSelectionState`的实例，可以控制一个文档中哪段文字被选中的状态。只支持iOS。

这个实例可以执行的一些函数:

- `blur()`
- `focus()`
- `update()`

参阅[`vendor/document/selection/DocumentSelectionState.js`](https://github.com/facebook/react-native/blob/master/Libraries/vendor/document/selection/DocumentSelectionState.js)源码中的`DocumentSelectionState`

#### `spellCheck`

如果设置为`false`，则禁用拼写检查的样式（比如错误拼写的单词下的红线）。默认值继承自`autoCorrect`。

#### `textContentType`

为键盘和系统提供有关用户输入内容的预期语义含义的信息。只支持iOS。

对于iOS11+，可以将`textContentType`设置为用户名或密码，以启用从设备钥匙串自动填充登录详细信息。

对于iOS12+，`newPassword`，可以用来表示用户可能想要保存在钥匙串中的新密码，而oneTimeCode可以用来表示一个字段可以由发送到SMS的代码自动填充。

要禁用自动填充，请将`textContentType`设置为none。

- `none`
- `URL`
- `addressCity`
- `addressCityAndState`
- `addressState`
- `countryName`
- `creditCardNumber`
- `emailAddress`
- `familyName`
- `fullStreetAddress`
- `givenName`
- `jobTitle`
- `location`
- `middleName`
- `name`
- `namePrefix`
- `nameSuffix`
- `nickname`
- `organizationName`
- `postalCode`
- `streetAddressLine1`
- `streetAddressLine2`
- `sublocality`
- `telephoneNumber`
- `username`
- `password`
- `newPassword`
- `oneTimeCode`



### 只支持Android的属性：

#### `disableFullscreenUI`

当值为false时, 如果 text input 的周围有少量可用空间的话（比如说，当手机横过来时），操作系统可能会将这个 text input 设置为全屏模式。当值为true时, 这个特性不可用，text input 就是普通的模式。默认为false。只支持Android。

#### `inlineImageLeft`

指定一个图片放置在左侧。图片必须放置在`/android/app/src/main/res/drawable`目录下，经过编译后按如下形式引用（无路径无后缀）：

```javascript
<TextInput
 inlineImageLeft='search_icon'
/>
```

只支持Android。

#### `inlineImagePadding`

给放置在左侧的图片设置padding样式。属性类型：number。只支持Android。

#### `numberOfLines`

设置输入框的行数。当multiline设置为true时使用它，可以占据对应的行数。只支持Android。

#### `returnKeyLabel`

 决定“确定”按钮显示的内容。，相当于 `returnKeyType`，只支持Android。

#### `textBreakStrategy`

在 Android API Level 23+ 的平台上设置文字断行策略, 可能值有`simple`, `highQuality`, `balanced`。默认值为`simple`。

#### `underlineColorAndroid`

文本框的下划线颜色(译注：如果要去掉文本框的边框，请将此属性设为透明transparent)。只支持Android。





## TextInput的方法

#### `clear()`

```javascript
clear();
```

清空输入框的内容。

#### `isFocused()`

```javascript
isFocused():
```

返回值表明当前输入框是否获得了焦点。