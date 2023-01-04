<template>
  <div>
    <div class="flex items-center justify-center h-screen w-screen shadow-2xl">
      <video
        ref="camera"
        id="video"
        autoPlay
        muted
        :class="{
          '`w-[300px] h-[300px] object-cover border-4 border-cyan-400` scale-${zoom} ': true,
          'rotate-image': cameraFlipped,
          'rounded-full': rounded,
          rounded: !rounded,
        }"
      />
    </div>
  </div>
</template>

<script>
import hotkeys from "hotkeys-js";
export default {
  data() {
    return {
      cameras: [],
      selectedCamera: null,

      cameraFlipped: true,
      rounded: false,
      zoom: 1,
    };
  },

  async mounted() {
    await this.getDevices();

    if (!this.selectedCamera) {
      this.selectedCamera = this.cameras[0].id;
    }

    this.startCamera();
    this.startShortcuts();
  },

  methods: {
    startShortcuts() {
      // flip camera
      // hotkeys("ctrl+i,command+i", (event, handler) => {
      //   event.preventDefault();
      //   this.cameraFlipped = !this.cameraFlipped;
      // });
      // zoom
      // hotkeys("ctrl+p,command+p", (event, handler) => {
      //   event.preventDefault();
      //   switch (handler.key) {
      //     case "ctrl+p":
      //       this.openGlobalSearch();
      //       break;
      //     case "command+p":
      //       this.openGlobalSearch();
      //       break;
      //   }
      // });
      hotkeys("cmd+*,ctrl+*", (event, handler) => {
        event.preventDefault();
        //
        switch (handler.key) {
          case "+":
            console.log("zoom in");
            break;
          case "-":
            console.log("zoom out");
            break;
        }
      });
      // zoom
      // hotkeys("ctrl+o,command+o", (event, handler) => {
      //   event.preventDefault();
      //   this.rounded = !this.rounded;
      // });
      // change camera source
      // hotkeys("ctrl+s,command+s", (event, handler) => {
      //   event.preventDefault();
      //   for (var i = 0; i < this.cameras.length; i++) {
      //     if (this.cameras[i].selected) {
      //       this.cameras[i].selected = false;
      //       if (this.cameras[i + 1]) {
      //         this.cameras[i + 1].selected = true;
      //         this.selectedCamera = this.cameras[i + 1];
      //       } else {
      //         this.cameras[0].selected = true;
      //         this.selectedCamera = this.cameras[0];
      //       }
      //       break;
      //     }
      //   }
      // });
    },
    startCamera() {
      navigator.mediaDevices
        .getUserMedia({
          video: {
            deviceId: this.selectedCamera,
          },
        })
        .then((stream) => {
          this.$refs.camera.srcObject = stream;
        })
        .catch((error) => {
          console.log("error");
        });
    },
    async getDevices() {
      const devices = await navigator.mediaDevices.enumerateDevices();
      const videoDevices = devices.filter(
        (device) => device.kind === "videoinput"
      );
      videoDevices.map((videoDevice) => {
        this.cameras.push({ id: videoDevice.deviceId, selected: false });
      });
    },
  },
};
</script>
<style>
.rotate-image {
  transform: scaleX(-1);
}
</style>
