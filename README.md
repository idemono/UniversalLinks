# Universal Links & Scheme Open App iOS(Deep Link)

语言: swift, 版本：4.2，XCode：10.1 <br>
写作时间：2019-02-11
# Scheme打开App
以前用Scheme方式打开app用类似于 `taobao://` 这种方式，配置如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211114429985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)
验证：在Safari输入链接：`zscheme://` 就可以打开app
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211114704642.png =426x838)
Alert提示host的内容, 在 `AppDelegate.swift` 添加如下代码：
```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        let message = url.host?.removingPercentEncoding
        let alertController = UIAlertController(title: "Incoming Message", message: message, preferredStyle: .alert)
        let okAction = UIAlertAction(title: "OK", style: .default, handler: nil)
        alertController.addAction(okAction)
        
        window?.rootViewController?.present(alertController, animated: true, completion: nil)
        
        return true
    }

```
浏览器访问链接 `textreader://Hello!`, 打开APP显示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211115501408.png =426x838)

### scheme打卡App的弊端
1. 任何App都可以用同一个名字，有些钓鱼App如果被安装了，可能被欺骗。
2. 没有安装App的情况下，跳转到安装页面需要通过JavaScript去判断，也就是必须打开网页，做进一步跳转。

# Universal Links说明
iOS 9 以后可以用Universal Links 打开App。当用户点击关联的host网页，无感地打开已经安装好的App(无需打开Safari)。如果没有安装App，就会打开链接到的网站。

Universal Links好处如下：
1. **Unique**. Unlike custom URL schemes, universal links can’t be claimed by other apps, because they use standard HTTP or HTTPS links to your website.

2. **Secure**. When users install your app, iOS checks a file that you’ve uploaded to your web server to make sure that your website allows your app to open URLs on its behalf. Only you can create and upload this file, so the association of your website with your app is secure.

3. **Flexible**. Universal links work even when your app is not installed. When your app isn’t installed, tapping a link to your website opens the content in Safari, as users expect.

4. **Simple**. One URL works for both your website and your app.

5. **Private**. Other apps can communicate with your app without needing to know whether your app is installed.

# Demo工程下载
下载地址：
https://koenig-media.raywenderlich.com/uploads/2018/09/UniversalLinks.zip
**UniversalLinks.xcodeproj** 运行如下：
![在这里插入图片描述](https://koenig-media.raywenderlich.com/uploads/2016/03/Navigation-668x500.png =600x500)

# Universal Links实现步骤
## 1. App配置.
### 配置bundleId, 必须为付费Apple developer， 否则没法测试。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211142249344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)

### 配置**Associated Domains** 为网站的host，网站必须为HTTPS协议。
```swift
applinks:universallinkszgpeace013001.herokuapp.com

```
路径为: UniversalLinks project > UniversalLinks target > Capabilities tab > Associated Domains.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211141722465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)

## 2. 创建JSON文件apple-app-site-association
创建没有扩展名的JSON文件apple-app-site-association，格式如下，
```json
{
	"applinks": {
		"apps": [],
		"details": [
			{
				"appID": "473VV338ZU.com.zgpeace.UniversalLinks",
				"paths": [ "*"]
			},
			{
				"appID": "473VV338ZU.com.zgpeace.UniversalLinksNew",
				"paths": [ "*"]
			}
		]
	}
}

```
**applinks** 表示app关联的网站信息。
**apps** 为空数据. 
**appID** 由team ID和app’s bundle ID构成. (`473VV338ZU.com.zgpeace.UniversalLinks` 需要改为你的信息才能用)
[点击链接查看team ID信息](https://developer.apple.com/membercenter) > Membership：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211144111663.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)
**paths** 为app和网站可以关联的host，如果为通配符*号，则表示整个网站都可以链接到app，如果指定paths，没有在范围内的则不跳转到app。



## 3. 上传文件apple-app-site-association到HTTPS服务器
没有HTTPS服务器，这里可以部署到免费的服务器Heroku。
请fork Github地址：
https://github.com/zgpeace/universal-links
选择分支：final，
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211144650842.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)
修改JSON配置文件apple-app-site-association的内容为你的信息，保存
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211145113731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)

编辑 README.md文件的部署链接为你fork下来的链接，注意是final分支，保存
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019021114491655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)
最后，点击README.md 的部署按钮，部署到Heroku服务器即可。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019021114530838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)
笔者部署的链接为：https://universallinkszgpeace013001.herokuapp.com/
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211145406843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pncGVhY2U=,size_16,color_FFFFFF,t_70)
配置文件的内容可以通过链接访问：https://universallinkszgpeace013001.herokuapp.com/apple-app-site-association
```json
{
	"applinks": {
		"apps": [],
		"details": [
			{
				"appID": "473VV338ZU.com.zgpeace.UniversalLinks",
				"paths": [ "*"]
			},
			{
				"appID": "473VV338ZU.com.zgpeace.UniversalLinksNew",
				"paths": [ "*"]
			}
		]
	}
}

```

## 4. APP接收跳转过来的事件
**AppDelegate.swift** 增加下面的方法1：
```swift
func presentDetailViewController(_ computer: Computer) {
  let storyboard = UIStoryboard(name: "Main", bundle: nil)
  
  guard 
    let detailVC = storyboard
      .instantiateViewController(withIdentifier: "DetailController")
        as? ComputerDetailController,
    let navigationVC = storyboard
      .instantiateViewController(withIdentifier: "NavigationController")
        as? UINavigationController 
  else { return }
  
  detailVC.item = computer
  navigationVC.modalPresentationStyle = .formSheet
  navigationVC.pushViewController(detailVC, animated: true)
}

```
代码解析：上面方法处理子页面的展示，present model的方式。

**AppDelegate.swift** 增加下面的方法2：
```swift
func application(
  _ application: UIApplication,
  continue userActivity: NSUserActivity,
  restorationHandler: @escaping ([UIUserActivityRestoring]?
) -> Void) -> Bool {
  
  // 1
  guard userActivity.activityType == NSUserActivityTypeBrowsingWeb,
    let url = userActivity.webpageURL,
    let components = URLComponents(url: url, resolvingAgainstBaseURL: true) else {
      return false
  }
  
  // 2
  if let computer = ItemHandler.sharedInstance.items
    .filter({ $0.path == components.path}).first {
    presentDetailViewController(computer)
    return true
  }
  
  // 3
  if let webpageUrl = URL(string: "http://rw-universal-links-final.herokuapp.com") {
    application.open(webpageUrl)
    return false
  }
  
  return false
}

```
代码解析：
1. 先校验userActivity是否为预期的字符. 最终你需要获取path的内容，跳转到相应的页面. 否则，你需要返回false，表示app不能处理该事件.

2. 用path去查找对应的页面处理，present相应的detail View展示，返回true.

3. 如果没有找到匹配的path，则用Safari打开URL.

# 验证
链接：https://universallinkszgpeace013001.herokuapp.com
因为url打开App需要跨域，验证可以把链接发送到邮箱，点击邮箱链接就可以打开。(如果把链接直接在Safari里面打开，是不会跳转的)
实现了Universal Links, Safari打开网站，系统检测到APP已经安装，头部则会出现跳转的链接，点击链接即可打开。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190211151843283.png =426x838) 
注意：如果一个host的apple-app-site-association配置多个APP，则按照顺序遍历，遍历到则打开APP并退出遍历。
# 总结
恭喜你！ 学会了Universal Links打开APP。

项目地址：
https://github.com/zgpeace/UniversalLinks

项目的starter 和 finished 各自实现了一个universal links，验证了apple-app-site-association配置2个APP的情况。

# 参考
https://www.raywenderlich.com/6080-universal-links-make-the-connection
https://www.appcoda.com/working-url-schemes-ios/
https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html
