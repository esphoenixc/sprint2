<template>
  <v-app id="inspire">
    <v-navigation-drawer app clipped right>
      <v-list v-if="isBoxDrawn">
        <v-list-item v-for="n in 1" :key="n" link>
          <v-list-item-content>
            <v-list-item-title>{{ n }} </v-list-item-title>
          </v-list-item-content>
          <v-text-field @keydown.enter="userEnter"></v-text-field>
        </v-list-item>
      </v-list>

      <!-- <v-list v-if="isUserInputEntered">
        <v-avatar
          v-for="(comment, i) in comments"
          :key="i"
          color="primary"
          size="56"
          >P</v-avatar
        >
      </v-list> -->
    </v-navigation-drawer>
    <v-main>
      <div id="container">
        <canvas id="canvas"> </canvas>
        <!-- <draggable> -->
        <div ref="contentZone" class="content-zone" :style="cssProps"></div>
        <div ref="contentZone2" class="content-zone2" :style="cssProps2"></div>
        <!-- </draggable> -->
        <video
          ref="video"
          id="videoId"
          src="./assets/videosample.mp4"
          controls
        ></video>
      </div>

      <!-- <HelloWorld /> -->
      <!-- <Card /> -->
    </v-main>
    <div class="wrapper">
      <div class="progress-bar"></div>

      <v-list v-if="isUserInputEntered">
        <v-avatar
          v-for="(comment, i) in comments"
          :key="i"
          color="primary"
          size="25"
          class="mt-5 mr-2"
          >P</v-avatar
        >
      </v-list>
    </div>
  </v-app>
</template>

<script>
import draggable from "vuedraggable";
import HelloWorld from "./components/HelloWorld";
import Card from "./components/Card.vue";

export default {
  name: "App",

  components: {
    HelloWorld,
    Card,
    draggable,
  },

  data() {
    return {
      drawer: null,
      comment: "",
      isBoxDrawn: false,
      isUserInputEntered: false,
      comments: [],

      isPainting: false,
      relStartX: 0,
      relStartY: 0,
      relEndX: 0,
      relEndY: 0,

      ctxLineWidth: 3,
      ctxLineCap: "square",
      ctxStrokeStyle: "red",

      video: null,
      canvas: null,
      ctx: null,
      rectValue: {},
      rectValue2: {},
      cssProps: "",
      cssProps2: "",
    };
  },

  methods: {
    userEnter(e) {
      this.comments.push(e.target.value);
      this.isBoxDrawn = false;
      this.isUserInputEntered = true;

      //1. 유저 아이콘을 Progress bar (video seek bar 밑으로 붙힌다)
      //2. 유저 아이콘을 그냥 붙이는게 아니라 특정 좌표, 시간대, %로 붙인다
      //3. 유저 아이콘이 Progress Bar (seek bar)가 resize되어도 잘 붙는다.
    },

    handleMouseDown(e) {
      this.isPainting = true;
      // this.canvas.style.cursor = "crosshair";
      console.log("pageX", e.pageX);
      console.log("pageY", e.pageY);

      this.relStartX = parseInt(e.pageX) / this.canvas.width;
      this.relStartY = parseInt(e.pageY) / this.canvas.height;

      console.log("mouseDown", this.relStartX, this.relStartY);
      // this.isBoxDrawn = false;
      // this.isBoxDrawn = true;
    },

    handleMouseUp(e) {
      //마우스 업 했을때 아무것도 안하는 layer 필요
      // if (this.isDrawClickRect) {
      //   this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      // }

      // this.canvas.style.cursor = "default";

      this.relEndX = parseInt(e.pageX) / canvas.width;
      this.relEndY = parseInt(e.pageY) / canvas.height;
      this.drawRect();

      console.log("mouseUp", this.relEndX, this.relEndY);
      //   drawRect();
      this.isBoxDrawn = !this.isBoxDrawn;
      this.isPainting = false;

      // this.drawClickRect();
    },

    // drawClickRect() {
    //   let currentStartX = this.relStartX * this.canvas.width;
    //   let currentStartY = this.relStartY * this.canvas.height;

    //   this.ctx.lineWidth = this.ctxLineWidth;
    //   this.ctx.lineCap = this.ctxLineCap;
    //   this.ctx.storkeStyle = this.ctxStrokeStyle;

    //   this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
    //   this.ctx.beginPath();

    //   this.rectValue2 = {
    //     width: 30,
    //     height: 30,

    //     startX: currentStartX,
    //     startY: currentStartY,
    //   };
    //   this.ctx.strokeRect(currentStartX, currentStartY, 30, 30);
    // },

    drawRect(w, h) {
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

      this.ctx.lineWidth = this.ctxLineWidth;
      this.ctx.lineCap = this.ctxLineCap;
      this.ctx.storkeStyle = this.ctxStrokeStyle;

      // this.rectValue = {
      //   width:
      //     currentEndX > currentStartX
      //       ? currentEndX - currentStartX
      //       : currentStartX - currentEndX,
      //   height:
      //     currentEndY > currentStartY
      //       ? currentEndY - currentStartY
      //       : currentStartY - currentEndY,
      //   // startX: currentStartX > currentEndX ? currentEndX : currentStartX,
      //   // startY: currentStartY > currentEndY ? currentEndY : currentStartY,
      //   // endX: currentEndX > currentStartX ? currentStartX : currentEndX,
      //   // endY: currentEndY > currentStartY ? currentStartY : currentEndY,
      //   // startX: currentStartX < currentEndX ? currentStartX : currentEndX,
      //   // startY: currentStartY < currentEndY ? currentStartY : currentEndY,
      //   // endX: currentStartX < currentEndX ? currentEndX : currentStartX,
      //   // endY: currentStartY < currentEndY ? currentEndY : currentStartY,
      //   // startX: currentStartX > currentEndX ? currentEndX : currentStartX,
      //   // startY: currentStartY > currentEndY ? currentEndY : currentStartY,
      //   // endX: currentEndX,
      //   // endY: currentEndY,
      //   startX: currentStartX,
      //   startY: currentStartY,
      //   endX: currentEndX,
      //   endY: currentEndY,
      // };
      this.rectValue = {
        width:
          currentEndX > currentStartX
            ? currentEndX - currentStartX
            : currentStartX - currentEndX,
        height:
          currentEndY > currentStartY
            ? currentEndY - currentStartY
            : currentStartY - currentEndY,
        startX: currentStartX,
        startY: currentStartY,
        endX: currentEndX,
        endY: currentEndY,
      };

      console.log(
        "drawRect()",
        // this.canvas.width,
        // this.canvas.height,
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

    this.$refs.video.addEventListener("loadedmetadata", () => {
      console.log("video loaded");
      this.canvas.height = this.video.offsetHeight;
      this.canvas.width = this.video.offsetWidth;

      this.canvas.addEventListener("mousedown", this.handleMouseDown);
      this.canvas.addEventListener("mouseup", this.handleMouseUp);
      this.canvas.addEventListener("mousemove", this.handleMouseMove);
    });

    // this.$refs.video.addEventListener("resize", (e) => {
    //   console.log("resize happening");
    //   console.log(this.canvas.height, this.canvas.width);
    //   console.log(this.video.height, this.video.width);

    //   this.canvas.height = this.video.offsetHeight;
    //   this.canvas.width = this.video.offsetWidth;
    //   this.drawRect();
    //   this.drawClickRect();
    // });

    window.addEventListener("resize", (e) => {
      console.log("resize happening");
      console.log(this.canvas.height, this.canvas.width);
      console.log(this.video.height, this.video.width);

      this.canvas.height = this.video.offsetHeight;
      this.canvas.width = this.video.offsetWidth;
      this.drawRect();
      // this.drawClickRect();
    });
  },

  destroyed() {
    console.log("Destroyed");
  },

  computed: {
    temp() {
      console.log("temp  ", this.rectValue);
      return "1";
    },
  },

  watch: {
    rectValue: {
      immediate: true,
      deep: true,
      handler(value) {
        console.log("value  ", value);
        console.log("contentZone  ", this.$refs.contentZone);

        let top =
          value.startY < value.endY
            ? value.startY
            : value.startY - value.height;
        let left =
          value.startX < value.endX ? value.startX : value.startX - value.width;

        console.log("top, left:", top, left);

        if (this.$refs.contentZone && this.$refs.contentZone !== null) {
          // this.cssProps = `--contentWidth: ${value.width}px !important, --contentHeight: ${value.height}px !important, --contentX: ${value.startX}, --contentY: ${value.startY}`;
          this.cssProps = `width: ${value.width}px !important; height: ${value.height}px !important; top: ${top}px !important; left: ${left}px !important`;
          // console.log("?????????????", value);
          // this.$refs.contentZone.style.width = value.width;
          // this.$refs.contentZone.style.height = value.height;
          // this.$refs.contentZone.style.top = value.startY;
          // this.$refs.contentZone.style.left = value.startX;
        }
      },
    },
    rectValue2: {
      immediate: true,
      deep: true,
      handler(value) {
        if (this.$refs.contentZone2 && this.$refs.contentZone2 !== null) {
          this.cssProps2 = `width: ${value.width}px !important; height: ${value.height}px !important; left: ${value.startX}px !important; top: ${value.startY}px !important`;
        }
      },
    },
  },
};
</script>

<style>
.content-zone {
  /* width: var(--contentWidth);
  height: var(--contentHeight);
  top: var(--contentY);
  left: var(--contentX); */
  stroke: red;
  z-index: 101;
  background: black;
  position: absolute;
}

.content-zone2 {
  stroke: red;
  z-index: 101;
  background: green;
  position: absolute;
}

.wrapper {
  width: 100%;
  height: 35px;
  background-color: #ccc;
  position: relative;
  border-radius: 7px;
  margin-bottom: 20%;
}

.wrapper .progress-bar {
  position: absolute;
  height: 100%;
  background-color: rgb(125, 44, 255);
  border-radius: 7px;
  animation: progress-animation 5s infinite;
}

@keyframes progress-animation {
  0% {
    width: 0%;
  }

  100% {
    width: 100%;
  }
}

/* * {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
} */

#canvas {
  /* /* width: 600px; */
  /* height: 673px; */
  /* max-width: 100%; */
  cursor: url("data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iNDIiIGhlaWdodD0iNDIiIHZpZXdCb3g9IjAgMCA0MiA0MiIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj4KPHBhdGggZD0iTTM4IDI1LjMzMzRDMzguMDA0NiAyNy4wOTMyIDM3LjU5MzQgMjguODI5MiAzNi44IDMwLjRDMzUuODU5MiAzMi4yODI0IDM0LjQxMyAzMy44NjU2IDMyLjYyMzMgMzQuOTcyNEMzMC44MzM1IDM2LjA3OTIgMjguNzcxIDM2LjY2NTkgMjYuNjY2NyAzNi42NjY3QzI0LjkwNjggMzYuNjcxMyAyMy4xNzA4IDM2LjI2MDEgMjEuNiAzNS40NjY3TDE3Ljc5NDcgMzYuNzM1MUMxNi4yMzEyIDM3LjI1NjMgMTQuNzQzNyAzNS43Njg4IDE1LjI2NDkgMzQuMjA1M0wxNi41MzMzIDMwLjRDMTUuNzM5OSAyOC44MjkyIDE1LjMyODcgMjcuMDkzMiAxNS4zMzMzIDI1LjMzMzRDMTUuMzM0MSAyMy4yMjkgMTUuOTIwOCAyMS4xNjY1IDE3LjAyNzYgMTkuMzc2OEMxOC4xMzQ0IDE3LjU4NyAxOS43MTc3IDE2LjE0MDggMjEuNiAxNS4yQzIzLjE3MDggMTQuNDA2NiAyNC45MDY4IDEzLjk5NTQgMjYuNjY2NyAxNEgyNy4zMzMzQzMwLjExMjUgMTQuMTUzNCAzMi43Mzc0IDE1LjMyNjQgMzQuNzA1NSAxNy4yOTQ1QzM2LjY3MzcgMTkuMjYyNiAzNy44NDY3IDIxLjg4NzYgMzggMjQuNjY2N1YyNS4zMzM0WiIgZmlsbD0iIzQ5NTM4MyIgZmlsbC1vcGFjaXR5PSIwLjUiIHN0cm9rZT0id2hpdGUiIHN0cm9rZS13aWR0aD0iMS4yNSIgc3Ryb2tlLWxpbmVjYXA9InJvdW5kIiBzdHJva2UtbGluZWpvaW49InJvdW5kIi8+CjxjaXJjbGUgY3g9IjIyLjAwMDYiIGN5PSIyNS41MDA2IiByPSIxLjI1MDU2IiBmaWxsPSJ3aGl0ZSIvPgo8Y2lyY2xlIGN4PSIyNy4wMDA4IiBjeT0iMjUuNTAwOCIgcj0iMS4yNTA4MyIgZmlsbD0id2hpdGUiLz4KPGNpcmNsZSBjeD0iMzIuMDAwOSIgY3k9IjI1LjUwMDkiIHI9IjEuMjUwODUiIGZpbGw9IndoaXRlIi8+CjxwYXRoIGZpbGwtcnVsZT0iZXZlbm9kZCIgY2xpcC1ydWxlPSJldmVub2RkIiBkPSJNNyAxQzYuNDQ3NzIgMSA2IDEuNDQ3NzIgNiAyVjZIMkMxLjQ0NzcyIDYgMSA2LjQ0NzcxIDEgN0MxIDcuNTUyMjggMS40NDc3MSA4IDIgOEg2VjEyQzYgMTIuNTUyMyA2LjQ0NzcyIDEzIDcgMTNDNy41NTIyOCAxMyA4IDEyLjU1MjMgOCAxMlY4SDEyQzEyLjU1MjMgOCAxMyA3LjU1MjI5IDEzIDdDMTMgNi40NDc3MiAxMi41NTIzIDYgMTIgNkg4VjJDOCAxLjQ0NzcyIDcuNTUyMjggMSA3IDFaIiBmaWxsPSIjNDk1MzgzIi8+CjxwYXRoIGQ9Ik02IDZWNi41QzYuMjc2MTQgNi41IDYuNSA2LjI3NjE0IDYuNSA2SDZaTTYgOEg2LjVDNi41IDcuNzIzODYgNi4yNzYxNCA3LjUgNiA3LjVWOFpNOCA4VjcuNUM3LjcyMzg2IDcuNSA3LjUgNy43MjM4NiA3LjUgOEg4Wk04IDZINy41QzcuNSA2LjI3NjE0IDcuNzIzODYgNi41IDggNi41VjZaTTYuNSAyQzYuNSAxLjcyMzg2IDYuNzIzODYgMS41IDcgMS41VjAuNUM2LjE3MTU3IDAuNSA1LjUgMS4xNzE1NyA1LjUgMkg2LjVaTTYuNSA2VjJINS41VjZINi41Wk0yIDYuNUg2VjUuNUgyVjYuNVpNMS41IDdDMS41IDYuNzIzODYgMS43MjM4NiA2LjUgMiA2LjVWNS41QzEuMTcxNTcgNS41IDAuNSA2LjE3MTU3IDAuNSA3SDEuNVpNMiA3LjVDMS43MjM4NiA3LjUgMS41IDcuMjc2MTQgMS41IDdIMC41QzAuNSA3LjgyODQzIDEuMTcxNTcgOC41IDIgOC41VjcuNVpNNiA3LjVIMlY4LjVINlY3LjVaTTYuNSAxMlY4SDUuNVYxMkg2LjVaTTcgMTIuNUM2LjcyMzg2IDEyLjUgNi41IDEyLjI3NjEgNi41IDEySDUuNUM1LjUgMTIuODI4NCA2LjE3MTU3IDEzLjUgNyAxMy41VjEyLjVaTTcuNSAxMkM3LjUgMTIuMjc2MSA3LjI3NjE0IDEyLjUgNyAxMi41VjEzLjVDNy44Mjg0MyAxMy41IDguNSAxMi44Mjg0IDguNSAxMkg3LjVaTTcuNSA4VjEySDguNVY4SDcuNVpNMTIgNy41SDhWOC41SDEyVjcuNVpNMTIuNSA3QzEyLjUgNy4yNzYxNCAxMi4yNzYxIDcuNSAxMiA3LjVWOC41QzEyLjgyODQgOC41IDEzLjUgNy44Mjg0MyAxMy41IDdIMTIuNVpNMTIgNi41QzEyLjI3NjEgNi41IDEyLjUgNi43MjM4NiAxMi41IDdIMTMuNUMxMy41IDYuMTcxNTcgMTIuODI4NCA1LjUgMTIgNS41VjYuNVpNOCA2LjVIMTJWNS41SDhWNi41Wk03LjUgMlY2SDguNVYySDcuNVpNNyAxLjVDNy4yNzYxNCAxLjUgNy41IDEuNzIzODYgNy41IDJIOC41QzguNSAxLjE3MTU3IDcuODI4NDMgMC41IDcgMC41VjEuNVoiIGZpbGw9IndoaXRlIi8+Cjwvc3ZnPgo=")
      6 6,
    auto;

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
  /* width: 1000px; */
  /* height: 64.285714285vw; */
  position: absolute;
  /* width: 100%; */
  /* height: auto; */
  max-width: 100%;

  /* top: 0; */
  /* left: 0; */
}
</style>
