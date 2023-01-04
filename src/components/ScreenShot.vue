<template></template>
<script>
import { desktopCapturer, remote } from "electron";
const { Menu } = remote;
export default {
  data() {
    return {
      videoOptionsMenu: null,
      inputSources: null,

      mediaRecorder: null,
      recordedChunks: [],
    };
  },
  mounted() {
    this.getVideoSources();
    this.videoOptionsMenu = Menu.buildFromTemplate(
      inputSources.map((source) => {
        return {
          label: source.name,
          click: () => selectSource(source),
        };
      })
    );

    this.videoOptionsMenu.popup();
  },
  methods: {
    async getVideoSources() {
      this.inputSources = await desktopCapturer.getSources({
        types: ["window", "screen"],
      });
    },

    async selectSource(source) {
      videoSelectBtn.innerText = source.name;

      const constraints = {
        audio: false,
        video: {
          mandatory: {
            chromeMediaSource: "desktop",
            chromeMediaSourceId: source.id,
          },
        },
      };

      // Create a Stream
      const stream = await navigator.mediaDevices.getUserMedia(constraints);

      // Preview the source in a video element
      videoElement.srcObject = stream;
      videoElement.play();

      // Create the Media Recorder
      const options = { mimeType: "video/webm; codecs=vp9" };
      this.mediaRecorder = new MediaRecorder(stream, options);

      // Register Event Handlers
      this.mediaRecorder.ondataavailable = handleDataAvailable;
      this.mediaRecorder.onstop = handleStop;
    },
  },
};
</script>
