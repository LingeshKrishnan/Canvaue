<template>
  <main>
    <img alt="Vue logo" src="./assets/logo.png" />
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <div class="fabric">
      <canvas ref="canvasRef" width="500" height="500"> </canvas>

    </div>
    <div class="container">
            <button @click="addNewRect()">Add Rectangle</button>
      <button @click="addImage()">Add Image</button>
      <button @click="addTextbox()">Add TextBox</button>
      <button @click="removeObject()">Delete Selected</button>
      <button @click="toJson()">TO Json</button>
      <button @click="loadJson()">Load From Json</button>
      <button @click="toImage()">To Image</button>
    </div>
  </main>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import { fabric } from "fabric";
import example from "./example.json";
var canvas = null;
export default {
  name: "App",
  methods: {
    addNewRect() {
      const rect = new fabric.Rect({
        top: 180,
        left: 100,
        width: 50,
        height: 50,
        fill: "#f3f345",
      });

      canvas.on("object:scaling", this.onObjectScaled);
      canvas.add(rect);
      canvas.setActiveObject(rect);
    },
    addImage() {
      fabric.Image.fromURL(this.myImage, (_img) => {
        const _me = _img.set({
          left: 0,
          top: 0,
          width: _img.width,
          height: _img.height,
        });
        canvas.on("object:scaling", this.onObjectScaled);
        canvas.add(_me);
        canvas.setActiveObject(_me);
      });
    },
    addTextbox() {
      const textBox = new fabric.Textbox("Enter your Text here", {
        left: 20,
        top: 20,
        fill: "Black",

        strokeWidth: 2,
      });
      canvas.add(textBox);
    },
    onObjectScaled() {
      const obj = canvas.getActiveObject();
      const width = obj.width;
      const height = obj.height;
      const scalex = obj.scaleX;
      const scaley = obj.scaleY;
      console.log("w", width, "h", height, "x", scalex, "y", scaley);
      obj.set({
        width: width + scalex,
        height: height + scaley,
        scaleX: 1,
        scaleY: 1,
      });
    },
    loadJson(){
      canvas.loadFromJSON(example, ()=> {
        canvas.renderAll();
      })
    },
    async toImage() {
      const ext ="jpeg";
      const base64 = canvas.toDataURL({
        format : ext,
        enableRetinaScaling : true
      });
      const link = document.createElement("a");
      link.href = base64;
      link.download = `example.${ext}`;
      link.click();
    },
    async toJson() {
      const json = canvas.toDatalessJSON("cliPath");
      const out = JSON.stringify(json, null, "\t");
      const blob = new Blob([out], { type: "text/plain" });
      // const clipboarddata = {[blob.type]: blob};

      const blobURL = URL.createObjectURL(blob);
      const a = document.createElement("a");
      a.href = blobURL;
      a.download = "example.json";
      a.click();
      URL.revokeObjectURL(blobURL);
    },
    removeObject() {
      canvas.remove(canvas.getActiveObject());
    },
  },
  mounted() {
    canvas = new fabric.Canvas(this.$refs.canvasRef, {
      isDrawingMode: false,
    });
  },
  components: {
    // HelloWorld
  },
  data() {
    return {
      myImage: "https://doc.qt.io/qt-6/images/qml-canvas-example.png",
    };
  },
};
</script>

<style>
.fabric {
  height: 500px;
  display: flex;
  align-items: center;
  justify-content: center;
}
canvas {
  border: 1px solid black;
  border-radius: 8px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
