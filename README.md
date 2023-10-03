# webRTC

https://github.com/googlecodelabs/webrtc-web

실시간 화상 채팅을 내 프로젝트에 접목하기 위해 webRTC를 학습하는 레포

# 학습 정리

## navigator

JavaScript에서 `navigator` 객체는 웹 브라우저의 정보와 상태를 제공하는 객체입니다. 이 객체는 다양한 프로퍼티와 메서드를 포함하고 있습니다. 여기에 몇 가지 중요한 `navigator` 객체의 프로퍼티와 메서드를 설명하겠습니다.

중요한 `navigator` 프로퍼티:

1. `navigator.userAgent`: 이 프로퍼티는 현재 웹 브라우저의 User-Agent 문자열을 반환합니다. 이 문자열은 브라우저의 종류와 버전 정보를 포함합니다.

2. `navigator.platform`: 이 프로퍼티는 현재 플랫폼(운영 체제) 정보를 제공합니다.

3. `navigator.language` 또는 `navigator.languages`: 이 프로퍼티(또는 메서드)는 사용자의 언어 환경 정보를 반환합니다.

4. `navigator.cookieEnabled`: 이 프로퍼티는 브라우저가 쿠키를 활성화했는지 여부를 나타내는 부울 값을 가집니다.

중요한 `navigator` 메서드:

1. `navigator.geolocation.getCurrentPosition()`: 이 메서드를 사용하면 사용자의 현재 위치 정보를 얻을 수 있습니다. 위치 정보는 콜백 함수를 통해 제공됩니다.

2. `navigator.mediaDevices.getUserMedia()`: 이 메서드는 웹 카메라와 마이크 같은 미디어 장치에 접근하기 위해 사용됩니다. 사용자에게 미디어 액세스 권한을 요청하고 스트림을 가져올 수 있습니다.

3. `navigator.clipboard.writeText()`: 이 메서드를 사용하여 클립보드에 텍스트를 복사하는 기능을 제공할 수 있습니다. 사용자의 동의가 필요할 수 있습니다.

4. `navigator.serviceWorker.register()`: Progressive Web App (PWA)에서 사용되는 메서드로, 서비스 워커를 등록하여 오프라인 작동 및 백그라운드 작업을 지원합니다.

이것은 일부 중요한 `navigator` 객체의 프로퍼티와 메서드에 대한 간략한 설명입니다. `navigator` 객체를 사용하여 브라우저의 환경 및 사용자 기기에 관한 정보를 얻고, 웹 애플리케이션을 개발할 때 이 정보를 활용할 수 있습니다.

## <video> `srcObject` property

`<video>` 태그의 `srcObject` 속성은 미디어 스트림을 설정하거나 변경하는 데 사용됩니다. 이 속성을 통해 JavaScript에서 동적으로 미디어 스트림을 `<video>` 요소에 할당할 수 있습니다. `srcObject` 속성은 미디어 스트림 객체를 값으로 받습니다.

예를 들어, 웹캠의 비디오 스트림을 `<video>` 요소에 표시하려면 다음과 같이 사용할 수 있습니다:

```javascript
// HTML 요소 가져오기
const videoElement = document.querySelector("video");

// 웹캠의 비디오 스트림 가져오기
navigator.mediaDevices
  .getUserMedia({ video: true })
  .then((stream) => {
    // 비디오 스트림을 <video> 요소의 srcObject에 할당
    videoElement.srcObject = stream;
  })
  .catch((error) => {
    console.error("비디오 스트림을 가져오는 동안 오류가 발생했습니다: ", error);
  });
```

이렇게 하면 웹캠의 비디오 스트림이 `<video>` 요소에 표시됩니다. `srcObject`를 사용하면 동적으로 비디오 스트림을 변경하거나 다른 미디어 스트림을 할당할 수 있으므로 실시간 비디오 스트림 처리나 웹RTC 기능을 구현할 때 유용합니다.
