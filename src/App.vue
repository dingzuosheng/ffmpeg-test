<template>
  <div id="app">
    <video
      id="video"
      controls
      src="./assets/video.webm"
      width="640"
      height="480"
    ></video>
    <video id="videocutRef" width="640" height="480"></video>
    <button @click="cut()">cut</button>
  </div>
</template>

<script>
import { createFFmpeg, fetchFile } from "@ffmpeg/ffmpeg";
const ffmpeg = createFFmpeg({
  corePath: "https://unpkg.com/@ffmpeg/core@0.10.0/dist/ffmpeg-core.js",
  log: true,
});
export default {
  name: "App",
  methode: {
    async cut() {
      await ffmpeg.load();
      ffmpeg.FS(
        "writeFile",
        "watermark.png",
        await fetchFile("./assets/watermark.png")
      );
      ffmpeg.FS("writeFile", "video.webm", fetchFile("./assets/video.webm"));
      await ffmpeg.run(
        "-i",
        "video.webm",
        "-i",
        "watermark.png",
        "-filter_complex",
        "overlay=x=65:y=355",
        "result.webm"
      );
      const data = ffmpeg.FS("readFile", "result.webm");
      const video = document.getElementById("videoCutRef");
      video.src = URL.createObjectURL(
        new Blob([data.buffer], "result.webm", {
          type: "video/webm",
        })
      );
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
