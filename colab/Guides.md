# Firebase + WebRTC Codelab

## 1. introduction

이 Codelab에서는 브라우저의 WebRTC API와 신호 전달을 위한 Cloud Firestore를 사용하여 간단한 영상 채팅 애플리케이션을 빌드하는 방법을 알아봅니다.
이 애플리케이션은 FirebaseRTC라고 하며 WebRTC 지원 애플리케이션 구축의 기본 사항을 알려주는 간단한 예제로 작동합니다.

> 참고:
> 신호를 보내는 또 다른 옵션은 Firebase 클라우드 `Messaging` 수 있습니다. 그러나 이는 현재
> Chrome에서만 지원되며 이 Codelab에서는 WebRTC를 지원하는 모든 브라우저에서 작동하는
> 솔루션에 중점을 둘 것입니다.

### 무엇을 배울 것인가

- WebRTC를 사용하여 웹 애플리케이션에서 영상 통화 시작
- Cloud Firestore를 사용하여 원격 상대방에게 신호 보내기

### What you'll need

Before starting this codelab, make sure that you've installed:

- npm which typically comes with Node.js - Node LTS is recommended

## 2. Create and Set up a Firebase Project

### Create a Firebase project

1. in the firebase console, click **Add project**, then select or enter a **Project name**.

Remember the project Id for your firebase project.

> note:
> If you go to the home page of your project, you can see the project ID and the
> other

1. click create project

the app the youre going to build uses two firebase services available on the web:

- cloud firebase to save structured data on the cloud and get instant notification when the data is updated
- firebase hosting to host and serve your static assets

for this specific codelab, you've already configured firebase hosting in the project you'll be cloning. however, for cloud firestore, we'll walk you through the configuration and enabling of the services using the firebase console.

### Enable Cloud Firestore

the app uses cloud firestore to save the chat messages and receive new chat messages.

you'll need to enable cloud firestore:

1. in the firebase console, open the **database** section
2. click **create database**
3. select **start in test mode** and click **next**

test mode ensure that you can freely write to the database during development. we'll make our database more secure later on this codelab.

## 3. Get The Sameple Code

```bash
git clone https://github.com/webrtc/FirebaseRTC
```

### import the starter app

Open the files in firebaseRTC in your editor and change them according to the instructions below. this directory contains
starting code for the codelab which consist of a not-yet functional app. we'll make it functional throughout the codelab.

## 4. install the Firebase Command Line interface

the Firebase CommandLine Interface allows you to serve your web app locally and deploy your web app to Firebase Hosting.

1. install CLI by running following command:

```bash
npm install -g firebase-tools
```

1. Verify that the CLI has been installed correctly by running the following command:

```bash
firebase --version
```

1. Authenticate the firebase tool by running the following command:

```bash
firebase login
```

앱의 로컬 디렉터리 및 파일에서 Firebase 호스팅에 대한 앱 구성을 가져오도록 웹 앱 템플릿을 설정했습니다. 하지만 이렇게 하려면 앱을 Firebase 프로젝트와 연결해야 합니다.

1. Associate your app with your firebase project by running the following command:

```bash
firebase use --add
```

2. when propmted, select your project ID, then give your firebase project an alias.

an alias is useful if you have multiple firebase projects. however, for this codelab, lets just use the alias of `default`.

1. Follow the remaining instruction in your command line.

## 5. run the local server

You're ready to actually start work on our app! Let's run the app locally!

1. Run the following Firebase CLI command: `sh firebase serve --only hosting`

2. Your command line should display the following response: `hosting: Local server: http://localhost:5000`

We're using the Firebase Hosting emulator to serve our app locally. The web app should now be available from http://localhost:5000.

1. Open your app at http://localhost:5000.

You should see your copy of FirebaseRTC which has been connected to your Firebase project.

The app has automatically connected to your Firebase project.

## 6. Creating a new Room

이 애플리케이션에서는 각 영상 채팅 세션을 방이라고 합니다. 사용자는 애플리케이션에서 버튼을 클릭하여 새 방을 만들 수 있습니다. 그러면 원격 상대방이 동일한 방에 참여하는 데 사용할 수 있는 ID가 생성됩니다. ID는 각 방의 Cloud Firestore에서 키로 사용됩니다.

각 방에는 제안과 답변 모두에 대한 `RTCSessionDescriptions`과 각 당사자의 ICE 후보자가 포함된 두 개의 별도 컬렉션이 포함됩니다.

첫 번째 작업은 발신자의 초기 제안을 사용하여 새 방을 만들기 위해 누락된 코드를 구현하는 것입니다. `public/app.js`를 열고 `// Add code for create a room here` 주석을 찾아 다음 코드를 추가합니다.

```javascript
const offer = await peerConnection.createOffer();
await peerConnection.setLocalDescription(offer);

const roomWithOffer = {
  offer: {
    type: offer.type,
    sdp: offer.sdp,
  },
};
const roomRef = await db.collection("rooms").add(roomWithOffer);
const roomId = roomRef.id;
document.querySelector("#currentRoom").innerText = `Current room is ${roomId} - You are the caller!`;
```

첫 번째 줄은 호출자의 제안을 나타내는 `RTCSessionDescription`을 생성합니다. 그런 다음 이는 로컬 설명으로 설정되고 마지막으로 Cloud Firestore의 새 방 개체에 기록됩니다.

다음으로, 데이터베이스의 변경 사항을 수신하고 호출 수신자의 답변이 추가된 시기를 감지합니다.

```javascript
roomRef.onSnapshot(async (snapshot) => {
  console.log("Got updated room:", snapshot.data());
  const data = snapshot.data();
  if (!peerConnection.currentRemoteDescription && data.answer) {
    console.log("Set remote description: ", data.answer);
    const answer = new RTCSessionDescription(data.answer);
    await peerConnection.setRemoteDescription(answer);
  }
});
```

이는 호출 수신자가 응답에 대한 `RTCSessionDescription`을 작성할 때까지 기다렸다가 이를 호출자 `RTCPeerConnection`에 대한 원격 설명으로 설정합니다.
