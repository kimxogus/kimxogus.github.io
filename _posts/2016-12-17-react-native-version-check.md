---
title: "react-native-version-check"
modified: 2016-12-17T19:30:00+09:00
categories:
    - React Native
tags:
    - 개발
    - react-native
    - mobile
    - android
    - ios
---
그간 작업하던 React Native 버전 체크 라이브러리인 ```react-native-version-check```의 1.0버전이 릴리즈 되었습니다!  

<div style="text-align:center;">
  <figure style="display:inline-block;width:100px;margin-right: 30px;">
    <a href="https://github.com/kimxogus/react-native-version-check">
      <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github/PNG/GitHub-Mark-120px-plus.png" alt="">
      <figcaption>GitHub</figcaption>
    </a>
  </figure>  
  <figure style="display:inline-block;width:100px;">
    <a href="https://www.npmjs.com/package/react-native-version-check">
      <img src="{{ site.url }}{{ site.baseurl }}/assets/images/npm/simple-logo/n-large.png" alt="">
      <figcaption>NPM</figcaption>
    </a>
  </figure>  
</div>

## 제작 배경
 앱이 지속적으로 업데이트 되는 와중에도 적지 않은 유저들이 업데이트를 하지 않고 구 버전에 머무르는데요. 메이저 업데이트가 이뤄지고, API가 바뀌는 등 구 버전에 대한 지원이 중단되는 경우 유저들에게 이를 알리고 업데이트 하도록 유도할 필요가 있습니다.  
 
단순히 구버전 유저들에게 업데이트 여부를 알려주기만 하는건 문제가 없지만, 이를 알리는 시점을 언제로 할지가 애매해지게 됩니다. 제출한 업데이트가 스토어에 반영되기까지는 최소 몇시간에서 며칠까지도 걸리는데 스토어에 반영되지 않은 업데이트를 안내하는 것은 유저들에게 혼란을 줄 수 있고 업데이트가 스토어에 반영되는 것을 직접 확인해서 안내하는 것은 아무래도 귀찮은 작업이니까요.

이를 해결할 수 있는 방법 중 하나가 스토어에 올라가있는 앱의 버전을 직접 확인하는 것입니다. 해당 앱의 앱스토어 혹은 마켓 페이지를 파싱하여 최신 버전 정보를 가져오는 것이죠. 이럴 경우 위의 두 문제를 모두 해결할 수 있습니다. 이를 이미 구현하여 코드를 공유하신 분 도 있습니다. ([Android Market Version Checker - 안드로이드 마켓 버전 확인하기 - 미르의 IT 정복기](http://itmir.tistory.com/524)) 다행히 플레이 마켓과 앱스토어 모두 버전 정보를 담고 있는 태그가 모두 ```itemprop="softwareVersion"``` 를 포함하고있기 때문에 위의 코드로 모두 파싱이 가능합니다. 그래서 이 코드를 이용하여 Android, iOS 모두를 지원하는 버전 체크 라이브러리인 ```react-native-version-check```을 만들게 되었습니다.  

## 설치
**react-native-version-check**은 버전 정보, 국가 정보(iOS AppStore 경로) 등을 가져오기 위해 네이티브 API를 사용하고 있습니다. 때문에 이를 추가하는 작업이 필요합니다. (제 깃헙의 README에 있는 설치법을 한글로 옮겨 적었습니다.)
  
### iOS
1. 프로젝트 폴더의 ios폴더를 XCode 프로젝트로 연다.
2. 프로젝트 네비게이터에서 _Libraries_ 우클릭
3. ```Add Files to [PROJECT_NAME]```
4. ```node_modules/react-native-version-check/ios/RNVersionCheck.xcodeproj``` 추가
5. 프로젝트의 _Build Phases_ > _Link Binary With Libraries_ 내 ```libRNVersionCheck.a``` 추가 

### Android
1. _android/settings.gradle_ 내 아래 구문 추가  
{% highlight gradle %}
...
include ':react-native-version-check'
project(':react-native-version-check').projectDir = new File(rootProject.projectDir,    '../node_modules/react-native-version-check/android')
{% endhighlight %}
   
2. _android/app/build.gradle_ 파일 내 _dependencies_에 _react-native-version-check_ 추가  
{% highlight gradle %}
dependencies {
    ...
    compile project(':react-native-version-check')
}
{% endhighlight %}
3. _android/app/src/main/java/[...]/MainApplication.java_ 내 _React Package_ 등록  
{% highlight java %}
import io.xogus.reactnative.versioncheck.RNVersionCheckPackage;  // <--- HERE

......

@Override
protected List<ReactPackage> getPackages() {
    ......
    new RNVersionCheckPackage()            // <------ HERE
    ......
}
{% endhighlight %}

## 사용법
자세한 Reference는 GitHub 저장소의 [README](https://github.com/kimxogus/react-native-version-check#methods)를 참고해주세요 :) 

- 라이브러리 임포트  
{% highlight js %}
import VersionCheck from "react-native-version-check";
{% endhighlight %}

- 패키지 확인하기  
{% highlight js %}
console.log(VersionCheck.getPackageName());
// com.your.app
{% endhighlight %}

- 현재 버전 확인하기
{% highlight js %}
console.log(VersionCheck.getCurrentVersion());
// 1.0.0
{% endhighlight %}

- 현재 빌드 번호 확인하기  
{% highlight js %}
console.log(VersionCheck.getCurrentBuildNumber());
// 10
{% endhighlight %}

- [iOS일 경우] App Store의 앱 이름, 앱 ID 등록하기
    - 스토어의 최신버전을 확인 하기 위해 스토어의 url을 생성해야하는데 플레이 마켓의 경우 패키지명만 있으면 되지만 앱스토어의 경우 국가, 앱 이름, 앱 ID가 필요합니다. 때문에 스토어 기준 최신버전 확인을 위해 이 값들을 지정해주셔야 합니다. (앱 내에서 1번만 등록)
    - 카카오톡의 App Store URL의 경우로 보자면 https://itunes.apple.com/KR/app/kakaotog-kakaotalk/id362057947 에서 _KR_ 이 국가 코드, _kakaotog-kakaotalk_ 이 앱 이름, _362057949_ 이 앱 ID입니다.
    - 제가 iOS 개발 경험이 없어 이 값들을 입력 없이 구하는 방법을 모르겠네요. 혹시 아시는 분 있다면 알려주시길 부탁드립니다!  
{% highlight js %}
VersionCheck.setAppID(APP_ID);
VersionCheck.setAppName(APP_NAME);
{% endhighlight %}  
    
- 최신 버전 확인하기 (앱스토어, 마켓 기준)  
{% highlight js %}
VersionCheck.getLatestVersion()
  .then(latestVersion => {
    console.log(latestVersion);
    // 2.0.0
  })
{% endhighlight %}

- 자체 URL이용하여 최신 버전 확인하기
    - _URL_ 과 _isomorphic-fetch_ 옵션을 이용하여 자체 API를 이용하실 수도 있습니다. 
    - iOS에서 자체 URL을 이용하여 최신버전 체크를 하는 경우, ```setAppID```와 ```setAppName```을 지정하지 않으셔도 됩니다.
{% highlight js %}
VersionCheck.getLatestVersion({
  url: "http://url.of/custom/api",
  fetchOptions: {
    method: "GET",
    headers: {
      "x-custom-param": "value"
    }
  }
}).then(latestVersion => {
  console.log(latestVersion);
  // 2.0.0
})
{% endhighlight %}

- 업데이트 필요 여부 확인하기
    - 버전 정보를 이용하여 업데이트 여부를 알 수 있습니다.  
{% highlight js %}
VersionCheck.needUpdate()
  .then(res => {
    console.log(res);
    // { isNeeded: true, currentVersion: "1.0.0", latestVersion: "1.1.0" }
  });
{% endhighlight %}

- 메이저 버전만 체크하기
    - _depth_ 를 이용하여 메이저 버전만을 이용하여 업데이트 여부를 판단할 수도 있습니다.
      
{% highlight js %}
VersionCheck.needUpdate({
  depth: 1,
  delimiter: "." // 기본값이므로 입력하지 않으셔도 됩니다.
}).then(res => {
  console.log(res);
  // { isNeeded: false, currentVersion: "1.0.0", latestVersion: "1.1.0" }
  // 버전의 첫 필드인 1이 같으므로 업데이트 할 필요가 없습니다.
});
{% endhighlight %}