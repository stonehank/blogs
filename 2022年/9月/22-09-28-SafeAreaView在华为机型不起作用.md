因为在react-native中，所有安卓都直接返回`<View />`

[https://github.com/facebook/react-native/blob/277a64f03ec2a5a5c833f15263b3c3fa45577cae/Libraries/Components/SafeAreaView/SafeAreaView.js#L29-L34](https://github.com/facebook/react-native/blob/277a64f03ec2a5a5c833f15263b3c3fa45577cae/Libraries/Components/SafeAreaView/SafeAreaView.js#L29-L34)

```
if (Platform.OS === 'android') {
  exported = View;
} else {
  exported = require('./RCTSafeAreaViewNativeComponent').default;
}

```

使用`react-native-safe-area-context`解决问题