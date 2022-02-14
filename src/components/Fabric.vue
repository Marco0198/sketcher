<template>
<div class="canvas-box">
  <canvas id="canvas" width="450" height="450"></canvas>
  <div uk-margin>
    <div uk-form-custom>
      <input type="file" @change="loadImage">
      <button class="uk-button uk-button-default" id="photo" type="button" tabindex="-1">photo</button>
    </div>
    <button class="uk-button uk-button-default" @click="addRect">add rectangle</button>
    <button class="uk-button uk-button-default" @click="addCircle">add circle</button>
  </div>
  <div uk-margin>
    <input type="text" class="uk-input uk-form-width-medium" placeholder="rnter your text here" v-model="text">
    <button class="uk-button uk-button-default" @click="addText()">Add text</button>
  </div>
  <p uk-margin>
    <button class="uk-button uk-button-primary" @click="download">download</button>
  </p>
</div>
</template>

<script>
import { fabric } from "fabric";
import "fabric-customise-controls";

export default {
  data() {
    return {
      canvas: null,
      text: ""
    };
  },
  methods: {
    addRect() {
      const rect = new fabric.Rect({
        top: 100,
        left: 100,
        width: 70,
        height: 70,
        fill: "red"
      });

      rect.set({
        hasRotatingPoint: false
      });

      this.canvas.add(rect);
      this.canvas.setActiveObject(rect);
    },
    addCircle() {
      const rect = new fabric.Circle({
        top: 100,
        left: 100,
        radius: 50,
        fill: "blue"
      });

      rect.set({
        hasRotatingPoint: false
      });

      this.canvas.add(rect);
      this.canvas.setActiveObject(rect);
    },
    addText() {
  
      const text = new fabric.Textbox(this.text, {
        top: 100,
        left: 100
      });
      text.set({
        hasRotatingPoint: false
      });
      this.canvas.add(text);
      this.canvas.setActiveObject(text);
      this.text = "";
    },
    download() {
      const url = this.canvas.toDataURL();
      const a = document.createElement("a");
      const e = document.createEvent("MouseEvent");
      a.download = "edit_test.jpg";
      a.href = url;

      e.initEvent(
        "click",
        true,
        true,
        window,
        1,
        0,
        0,
        0,
        0,
        false,
        false,
        false,
        false,
        0,
        null
      );
      a.dispatchEvent(e);
    },
    loadImage(e) {
      const files = e.target.files || e.dataTransfer.files;
      if (!files.length) {
        return;
      }
      const file = files[0];
      const reader = new FileReader();
      reader.onload = f => {
        const data = f.target.result;
        fabric.Image.fromURL(data, img => {
          const to_scale = this.getScale(img);
          const image = img.scale(to_scale).set({
            top: this.canvas.height / 2,
            left: this.canvas.width / 2,
            originX: "center",
            originY: "center",
            hasRotatingPoint: false
          });
          this.canvas.add(image).renderAll();
        });
      };
      reader.readAsDataURL(file);
    },
    getScale(img) {
      const per_w = img.width / this.canvas.width;
      const per_h = img.height / this.canvas.height;
      const per_diff = per_w - per_h;

      if (img.width >= this.canvas.width || img.height >= this.canvas.height) {
        if (per_diff > 0) {
          return 1 / per_w * 0.95;
        } else {
          return 1 / per_h * 0.95;
        }
      }

      return 0.9;
    }
  },
  mounted() {
    this.canvas = new fabric.Canvas("canvas");

    fabric.Canvas.prototype.customiseControls(
      {
        tl: {
          action: "remove",
          cursor: "pointer"
        },
        tr: {
          action: "rotate",
          cursor: "pointer"
        },
        br: {
          action: "scale"
        }
      },
      () => {
        this.canvas.renderAll();
      }
    );

    // fabric.Object.prototype.setControlsVisibility({
    //   mt: true,
    //   ml: true,
    //   mr: true,
    //   mb: true,
    //   bl: true
    // });

    fabric.Object.prototype.customiseCornerIcons(
      {
        settings: {
          borderColor: "black",
          cornerSize: 25,
          cornerShape: "circle",
          cornerBackgroundColor: "white",
          cornerPadding: 10
        },
        tl: {
          icon:
            "//use.fontawesome.com/releases/v5.0.9/svgs/regular/times-circle.svg"
        },
        tr: {
          icon: "//use.fontawesome.com/releases/v5.0.9/svgs/solid/redo-alt.svg"
        },
        br: {
          icon:
            "//use.fontawesome.com/releases/v5.0.9/svgs/solid/expand-arrows-alt.svg"
        }
      },
      () => {
        this.canvas.renderAll();
      }
    );

    this.canvas.on("selection:created", e => {
      this.canvas.bringToFront(e.target);
      this.canvas.renderAll();
    });
  }
};
</script>

<style>
canvas {
  border: 1px solid #ccc;
}
.canvas-container {
  margin: 0 auto;
}
</style>
