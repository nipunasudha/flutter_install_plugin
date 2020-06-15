# install_apk_plugin

Fork from https://github.com/hui-z/flutter_install_plugin

## Usage

To use this plugin, add `install_apk_plugin` as a dependency in your pubspec.yaml file. 

```
dependencies:
  install_apk_plugin: ^1.0.0
```

### Example

For Android, you may need to request permission for READ_EXTERNAL_STORAGE to read the apk file. In the example, I used the `permission_handler` [plugin](https://pub.dartlang.org/packages/permission_handler).

```dart
import 'dart:async';

import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:install_apk_plugin/install_apk_plugin.dart';
import 'package:permission_handler/permission_handler.dart';

void main() => runApp(new MyApp());

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => new _MyAppState();
}

class _MyAppState extends State<MyApp> {
  String _appUrl = '';
  String _apkFilePath = '';

  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      home: new Scaffold(
        appBar: new AppBar(
          title: const Text('Plugin example app'),
        ),
        body: new Column(
          children: <Widget>[
            TextField(
              decoration: InputDecoration(
                  hintText:
                      'apk file path to install. Like /storage/emulated/0/demo/update.apk'),
              onChanged: (path) => _apkFilePath = path,
            ),
            FlatButton(
                onPressed: () {
                  onClickInstallApk();
                },
                child: Text('install')),
            TextField(
              decoration:
                  InputDecoration(hintText: 'URL for app store to launch'),
              onChanged: (url) => _appUrl = url,
            ),
            FlatButton(
                onPressed: () => onClickGotoAppStore(_appUrl),
                child: Text('gotoAppStore'))
          ],
        ),
      ),
    );
  }

  void onClickInstallApk() async {
    if (_apkFilePath.isEmpty) {
      print('make sure the apk file is set');
      return;
    }
    Map<PermissionGroup, PermissionStatus> permissions =
        await PermissionHandler().requestPermissions([PermissionGroup.storage]);
    if (permissions[PermissionGroup.storage] == PermissionStatus.granted) {
      InstallPlugin.installApk(_apkFilePath, 'com.zaihui.installpluginexample')
          .then((result) {
        print('install apk $result');
      }).catchError((error) {
        print('install apk error: $error');
      });
    } else {
      print('Permission request fail!');
    }
  }

  void onClickGotoAppStore(String url) {
    url = url.isEmpty
        ? 'https://itunes.apple.com/cn/app/%E5%86%8D%E6%83%A0%E5%90%88%E4%BC%99%E4%BA%BA/id1375433239?l=zh&ls=1&mt=8'
        : url;
    InstallPlugin.gotoAppStore(url);
  }
}

```

For help getting started with Flutter, view our online
[documentation](https://flutter.io/).

For help on editing plugin code, view the [documentation](https://flutter.io/developing-packages/#edit-plugin-package).
