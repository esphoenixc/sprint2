<template>
  <div id="container">
    <canvas id="canvas"></canvas>
    <video id="videoId" src="../assets/videosample.mp4" controls></video>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isPainting: false,
      relStartX: 0,
      relStartY: 0,
      relEndX: 0,
      relEndY: 0,

      ctxLineWidth: 5,
      ctxLineCap: "square",
      ctxStrokeStyle: "black",

      video: null,
      canvas: null,
      ctx: null,
    };
  },

  methods: {
    handleMouseDown(e) {
      this.isPainting = true;
      this.canvas.style.cursor = "crosshair";
      console.log("pageX", e.pageX);
      console.log("pageY", e.pageY);

      this.relStartX = parseInt(e.pageX) / this.canvas.width;
      this.relStartY = parseInt(e.pageY) / this.canvas.height;

      console.log("mouseDown", this.relStartX, this.relStartY);
    },

    handleMouseUp(e) {
      this.isPainting = false;
      this.canvas.style.cursor = "default";

      //   relEndX = parseInt(e.pageX) / canvas.width;
      //   relEndY = parseInt(e.pageY) / canvas.height;
      console.log("mouseUp", this.relEndX, this.relEndY);
      //   drawRect();
    },

    drawRect() {
      let currentStartX = this.relStartX * this.canvas.width;
      let currentStartY = this.relStartY * this.canvas.height;
      let currentEndX = this.relEndX * this.canvas.width;
      let currentEndY = this.relEndY * this.canvas.height;

      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.ctx.beginPath();

      this.ctx.strokeRect(
        currentStartX,
        currentStartY,
        currentEndX - currentStartX,
        currentEndY - currentStartY
      );

      console.log(
        "drawRect()",
        this.canvas.width,
        this.canvas.height,
        currentStartX,
        currentStartY,
        currentEndX,
        currentEndY
      );
    },

    handleMouseMove(e) {
      if (!this.isPainting) return;

      this.relEndX = parseInt(e.pageX) / this.canvas.width;
      this.relEndY = parseInt(e.pageY) / this.canvas.height;

      this.drawRect();
    },
  },

  mounted() {
    this.video = document.querySelector("#videoId");
    this.canvas = document.querySelector("#canvas");
    this.ctx = this.canvas.getContext("2d");
    console.log(this.video, this.canvas);
    console.log(this.ctx);

    window.addEventListener("load", () => {
      console.log("Loading");
      console.log(this.video, this.canvas);
      console.log(this.canvas.height, this.canvas.width);
      console.log(this.video.height, this.video.width);
      this.canvas.height = this.video.offsetHeight;
      this.canvas.width = this.video.offsetWidth;
      this.canvas.addEventListener("mousedown", this.handleMouseDown);
      this.canvas.addEventListener("mouseup", this.handleMouseUp);
      this.canvas.addEventListener("mousemove", this.handleMouseMove);
    });

    window.addEventListener("resize", (e) => {
      console.log("resize happening");
      console.log(this.canvas.height, this.canvas.width);
      console.log(this.video.height, this.video.width);
      this.canvas.height = this.video.offsetHeight;
      this.canvas.width = this.video.offsetWidth;
      this.drawRect();
      //   console.log(canvas.height, canvas.width);
      //   console.log("resize");
    });
  },

  destroyed() {
    console.log("Destroyed");
  },
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#canvas {
  /* width: 600px;
  height: 600px; */
  border: 2px solid red;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 100;
}

#container {
  position: relative;
}

#videoId {
  /* width: 100vw;
  height: 64.285714285vw; */
  position: absolute;
  width: 100%;
  height: auto;

  /* top: 0; */
  /* left: 0; */
}
</style>
