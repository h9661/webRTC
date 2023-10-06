## What is RTCPeerConnection?

`RTCPeerConnection`은 WebRTC API의 핵심입니다. 이 인터페이스는 웹 브라우저 간에 피어 간 연결을 설정하고 유지하는 데 사용됩니다. `RTCPeerConnection` 인터페이스는 미디어 스트림을 교환하고 피어 간에 데이터를 전송하는 데 사용됩니다.

WebRTC peer간 설정하는 데에는 3가지 단계가 있습니다.

1. RTCPeerConnection 객체를 각각의 peer에서 생성합니다. 그리고 각각의 peer에서 생성한 객체에 local stream을 추가합니다.
2. 네트워크 정보를 얻고, 교환합니다: 잠재적 연결 endpoint는 ICE 후보라고 알려져 있습니다.
3. local, remote description을 얻고, 교환합니다: 이것은 SDP라고 알려져 있습니다.

## What is adapter.js?

adapter.js는 WebRTC API를 사용하는 데 필요한 브라우저 간 차이를 해결하기 위한 라이브러리입니다. 이 라이브러리는 브라우저 간의 호환성 문제를 해결하고, WebRTC API를 사용하여 오디오, 비디오 및 데이터를 교환할 수 있도록 도와줍니다. adapter.js는 WebRTC API를 사용하는 데 필수적인 기능을 제공하며, 브라우저 간의 호환성 문제를 해결하는 데 도움이 됩니다.

## 핵심 함수

### `startAction()`

```js
function startAction() {
  startButton.disabled = true;
  navigator.mediaDevices
    .getUserMedia(mediaStreamConstraints)
    .then(gotLocalMediaStream)
    .catch(handleLocalMediaStreamError);
  trace("Requesting local stream.");
}
```

이 함수는 step1에서 했던 것과 같다. `getUserMedia()`를 호출하여 local stream을 얻는다. 그리고 `gotLocalMediaStream()`을 호출하여 local stream을 처리한다.

```js
function gotLocalMediaStream(mediaStream) {
  localVideo.srcObject = mediaStream;
  localStream = mediaStream;
  trace("Received local stream.");
  callButton.disabled = false; // Enable call button.
}
```

### `callAction()`

```js
function callAction() {
  callButton.disabled = true;
  hangupButton.disabled = false;
  trace("Starting call.");
  startTime = window.performance.now();

  const videoTracks = localStream.getVideoTracks();
  const audioTracks = localStream.getAudioTracks();
  if (videoTracks.length > 0) {
    trace(`Using video device: ${videoTracks[0].label}.`);
  }
  if (audioTracks.length > 0) {
    trace(`Using audio device: ${audioTracks[0].label}.`);
  }

  const servers = null; // Allows for RTC server configuration.
  localPeerConnection = new RTCPeerConnection(servers);
  trace("Created local peer connection object localPeerConnection.");

  localPeerConnection.addEventListener("icecandidate", handleConnection);
  localPeerConnection.addEventListener(
    "iceconnectionstatechange",
    handleConnectionChange
  );

  remotePeerConnection = new RTCPeerConnection(servers);
  trace("Created remote peer connection object remotePeerConnection.");

  remotePeerConnection.addEventListener("icecandidate", handleConnection);
  remotePeerConnection.addEventListener(
    "iceconnectionstatechange",
    handleConnectionChange
  );
  remotePeerConnection.addEventListener("addstream", gotRemoteMediaStream);

  localPeerConnection.addStream(localStream);
  trace("Added local stream to localPeerConnection.");

  trace("localPeerConnection createOffer start.");
  localPeerConnection
    .createOffer(offerOptions)
    .then(createdOffer)
    .catch(setSessionDescriptionError);
}
```

GitHub Copilot: 이 함수는 WebRTC API를 사용하여 peer-to-peer 연결을 설정하는 데 사용됩니다. `callButton`을 클릭하면 호출되며, `localStream`에서 비디오 및 오디오 트랙을 가져와 `localPeerConnection` 객체를 만듭니다. 그런 다음 `remotePeerConnection` 객체를 만들고, 두 개의 객체 간에 ICE 후보를 교환하고 SDP를 교환하여 연결을 설정합니다. 이 함수는 `createOffer()` 메서드를 호출하여 로컬 피어 연결의 SDP offer를 생성하고, `createdOffer()` 함수를 호출하여 생성된 offer를 처리합니다.
