# 让你的应用程序用覆盖图反应原生菜单的小技巧

> 原文：<https://dev.to/gaserd/small-trick-for-your-app-to-react-native---side-menu-with-overlay-52be>

[![Side Menu](../Images/b9d4dca53ad4c4830cda4ccb307c1334.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LgCeQ1oP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5lgsd81w3d77fdgsusbj.png)

大家好！

我喜欢 React Native，并写了几篇文章:

*   【React Native 的分析
*   [移动生活](https://dev.to/gaserd/mobile-development-in-real-life-5gjd)

是的，这是最受欢迎的问题，如何从侧菜单叠加视图。

SideMenu.js

```
import React, { Component } from 'react';
import {
    View, 
    Text 
} from 'react-native';

import MenuList from './containers/MenuList'
import MenuOverlay from './containers/MenuOverlay'

import styles from './style/SideMenuStyle'

export default class SideMenu extends Component {
    render() {        
        let { 
            navigation,
            onToggleMenu 
        } = this.props

        return (
            <View style={styles.container}>
                <MenuOverlay 
                    onToggleMenu={onToggleMenu}
                    navigation={navigation}
                />
                <View style={styles.menu}>
                    <MenuList 
                        onToggleMenu={onToggleMenu}
                        navigation={navigation} 
                    />
                </View>
            </View>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

。/style/SideMenuStyle

```
import { StyleSheet, Dimensions } from 'react-native';

let width = Dimensions.get('window').width
let height = Dimensions.get('window').height

export default styles = StyleSheet.create({
    container : {
        flex: 1,
        position : 'absolute',
        left: 0,
        top: 0,
        width : width, 
        height : height,
        paddingTop : 10,
        paddingLeft : 10,
        paddingRight : 10,
        paddingBottom : 10
    },
    menu: {
        flex: 1,
        backgroundColor: '#FFF',
        position : 'absolute',
        left: 0,
        top: 0,
        width : width * 0.8, 
        height : height,
        paddingTop : 10,
        paddingLeft : 10,
        paddingRight : 10,
        paddingBottom : 10
    },
    menuItem : {
        paddingTop : 10
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

MenuOverlay.js

```
import React, { Component } from 'react';
import {
    TouchableHighlight,
    Text
} from 'react-native';

import styles from '../style/MenuOverlayStyle'

export default class MenuOverlay extends Component {

    render() {

        return (
            <TouchableHighlight 
                onPress={() => {
                    this.props.onToggleMenu()
                }}
                style={styles.overlay}>
                <Text></Text>
            </TouchableHighlight>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

../style/MenuOverlayStyle

```
import { StyleSheet, Dimensions } from 'react-native';

let width = Dimensions.get('window').width
let height = Dimensions.get('window').height

export default styles = StyleSheet.create({
    overlay: {
        backgroundColor: 'rgba(52, 52, 52, 0.8)',
        position : 'absolute',
        left: 0,
        top: 0,
        width : width, 
        height : height,
        paddingTop : 10,
        paddingLeft : 10,
        paddingRight : 10,
        paddingBottom : 10
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

它是如何工作的？

[![chick](../Images/b7e29883fafec3d6b2467ba0be5c7f96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--22TVw2fA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://im.ezgif.com/tmp/ezgif-1-d597dfb718.gif)