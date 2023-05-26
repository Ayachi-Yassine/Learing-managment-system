<template>
  <div>
    <div id="root"></div>
    <button @click="stopVideoCall" >Stop Video Call</button>
    
  </div>
</template>

<script>
export default {
  mounted() {
    const zegoCloudScript = document.createElement('script');
    zegoCloudScript.src = 'https://unpkg.com/@zegocloud/zego-uikit-prebuilt/zego-uikit-prebuilt.js';
    zegoCloudScript.onload = () => {
      this.initializeVideoCall();
    };

    document.head.appendChild(zegoCloudScript);

    this.$router.beforeEach((to, from, next) => {
      // Execute stopVideoCall function when navigating away from the VideoCall component 
      if (to.name !== 'VideoCall') {
        this.stopVideoCall();
      }
      next();
    });
  },
  methods: {
    initializeVideoCall() {
      function getUrlParams(url) {
        let urlStr = url.split('?')[1];
        const urlSearchParams = new URLSearchParams(urlStr);
        const result = Object.fromEntries(urlSearchParams.entries());
        return result;
      }

      const roomID = getUrlParams(window.location.href)['roomID'] || (Math.floor(Math.random() * 10000) + "");
      const userID = Math.floor(Math.random() * 10000) + "";
      const userName = "userName" + userID;
      const appID = 2020986907;
      const serverSecret = "b4617a301a5ad487ccbeeb2dee21f668";
      const kitToken = ZegoUIKitPrebuilt.generateKitTokenForTest(appID, serverSecret, roomID, userID, userName);

      const zp = ZegoUIKitPrebuilt.create(kitToken);
      zp.joinRoom({
        container: document.querySelector("#root"),
        sharedLinks: [{
          name: 'Personal link',
          url: window.location.protocol + '//' + window.location.host + window.location.pathname + '?roomID=' + roomID,
        }],
        scenario: {
          mode: ZegoUIKitPrebuilt.VideoConference,
        },
        turnOnMicrophoneWhenJoining: true,
        turnOnCameraWhenJoining: true,
        showMyCameraToggleButton: true,
        showMyMicrophoneToggleButton: true,
        showAudioVideoSettingsButton: true,
        showScreenSharingButton: true,
        showTextChat: true,
        showUserList: true,
        maxUsers: 50,
        layout: "Auto",
        showLayoutButton: true,
      });
    },
    stopVideoCall() {
      // Add logic to stop the video call and perform any cleanup
      // For example:
      const zp = ZegoUIKitPrebuilt.create(kitToken);
      zp.stop();

      // Navigate back to the home page
      this.$router.push('/');
    },
  },
};
</script>

<style scoped>
#root {
  width: 100vw;
  height: 100vh;
}
</style>
