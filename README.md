# lexin



![](https://antzyun.oss-cn-beijing.aliyuncs.com/20201025174629.png)


> 我不歧视女性，也不歧视胖子，但你，是个女胖子。



> 使用方式：secrets中添加USERNAME用户名，PASSWORD密码，STEP目标步数



平常作息太不规律，想着去健身房跑跑步，减减肥，毕竟每天坐着不活动容易养膘。

每天锻炼确实很考验毅力，坚持几天之后我就在想锻炼的意义了，先来说说不锻炼的意义。

1. 好看的锁骨千篇一律，有趣的肚子弹来弹去。
2. 肥胖只是人体的属性之一，对于所谓的机器学习来说，你肥胖的属性只是机器在计算预测你行为数据时的众多向量维度之一而已。
3. 你身上至少有二十斤肥肉是靠”不能浪费“四个字得来。
4. 所谓人间蒸发，所以人是液体，同理，灵魂升华，所以灵魂是固体，胖子才是有趣的灵魂。

那么有什么方法既可以不用锻炼，又可以在精神层面达到锻炼的满足感呢？

当然是用脚本刷步数了。

关于刷步数这件事，微信支付宝QQ等本身的步数校验方法非常完善，很难通过他们的接口来刷步数，但是他们提供来第三方应用同步的接口，而这些应用的数据同步接口都是可以抓包获取，并且自己用爬虫来提交目标步数的。

今天推荐的方法就是使用乐心运动的数据同步，来同步到微信支付宝等应用。

应用下载地址

http://app.mi.com/details?id=gz.lifesense.weidong

下载应用后，使用手机号注册，在设置中添加密码，数据同步中选中要同步的app。

代码仓库地址：

https://github.com/CasterWx/lexin

将该仓库fork，然后和前文B站自动投币中方式类似，在secrets中添加三个环境变量，分别是USERNAME用户名，PASSWORD密码，STEP目标步数。

此处为了图方便，用了官方的打包脚本。

```
name: Upload Python Package

on:
  push:
    branches: main
  pull_request:
    branches: main
  workflow_dispatch:
  schedule:
    - cron: '10 2 * * *'
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.x'
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install setuptools wheel twine
    - name: Build and publish
      env:
        TWINE_USERNAME: ${{ secrets.USERNAME }}
        TWINE_PASSWORD: ${{ secrets.PASSWORD }}
        TWINE_STEP: ${{ secrets.STEP }}
      run: |
        python main.py ${TWINE_USERNAME} ${TWINE_PASSWORD} ${TWINE_STEP}
```

然后在Action中点击run即可，每日默认六点十分会自动执行。

如下可以看到步数修改成功。

![image.png](https://www.aloli.icu/upload/2020/10/image-c7d13b90e46d4b7497b9ea1519661f90.png)


![image.png](https://www.aloli.icu/upload/2020/10/image-3a70c1d574784f84a36ca90eeaf95b30.png)

实现可以参考仓库内代码，只是一个简单的模拟登录，以及数据提交，此外就是GitHub Action。

两个请求的构造如下。

```
# 登录
url = 'https://sports.lifesense.com/sessions_service/login?systemType=2&version=4.6.7'
data = {'loginName': self.username, 'password': hashlib.md5(self.password.encode('utf8')).hexdigest(), 'clientId': '49a41c9727ee49dda3b190dc907850cc', 'roleType': 0, 'appType': 6}
headers = {'Content-Type': 'application/json; charset=utf-8', 'User-Agent': 'Dalvik/2.1.0 (Linux; U; Android 7.1.2; LIO-AN00 Build/LIO-AN00)'}
      
```

```
# 修改步数
url = 'https://sports.lifesense.com/sport_service/sport/sport/uploadMobileStepV2?systemType=2&version=4.6.7'
data = {'list': [{'DataSource': 2, 'active': 1, 'calories': int(self.step/4), 'dataSource': 2, 'deviceId': 'M_NULL', 'distance': int(self.step/3), 'exerciseTime': 0, 'isUpload': 0, 'measurementTime': time.strftime('%Y-%m-%d %H:%M:%S'), 'priority': 0, 'step': self.step, 'type': 2, 'updated': int(round(time.time() * 1000)), 'userId': login_result[0]}]}
headers = {'Content-Type': 'application/json; charset=utf-8', 'Cookie': 'accessToken=%s' % login_result[1]}
```

既然步数这么高了，那么某种意义上大脑就会觉得自己锻炼了，如果这还不瘦就太没天理了。

扫码关注公众号～

![](https://antzyun.oss-cn-beijing.aliyuncs.com/20201025175233.png)