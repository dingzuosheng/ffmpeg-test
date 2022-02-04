<template>
  <div id="app">
    <video
      id="video"
      controls
      src="../public/video.webm"
      width="640"
      height="480"
    ></video>
    <video id="videocutRef" width="640" height="480" controls></video>
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
  methods: {
    async cut() {
      await ffmpeg.load();
      console.log('load ist gestartet');
      ffmpeg.FS(
        "writeFile",
        "watermark.PNG",
        await fetchFile("./watermark.PNG")
        // await fetchFile("/c/Materna-Projekt2/ffmpeg-test/src/assets/watermark.PNG")

      );
      console.log('writeFile von watermark.PNG ist gestartet');
      ffmpeg.FS("writeFile", "video.webm", await fetchFile("./video.webm"));
      console.log('writeFile von video.webm ist gestartet');
      await ffmpeg.run(
        "-i",
        "video.webm",
        "-i",
        "watermark.PNG",
        "-filter_complex",
        "overlay=x=65:y=355",
        "result.webm"
      );
      console.log('run ist gestartet');
      const data = ffmpeg.FS('readFile', 'result.webm');
      console.log('readFile ist gestartet');
      const video = document.getElementById("videocutRef");
      console.log(data)
      console.log(data.buffer)
      video.src = URL.createObjectURL(
        new Blob([data.buffer],{
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
