<template>
  <div class="col-lg-6 col-m-12 mb-4">
    <div class="card bg-default">
      <div class="card-header font-weight-bold">Input</div>
      <div class="card-body input">
        <div class="canvas-container">
          <canvas id="drawing-board"
                  v-on:mousedown="mouse_down"
                  v-on:mousemove="mouse_move"
                  v-on:mouseup="mouse_up"
                  v-on:mouseleave="mouse_leave"
          >

          </canvas>
        </div>
        <div class="button-container">
          <button @click="clear" v-bind:class="{disabled: !can_predict}" class="btn btn-secondary">
            Clear
          </button>
          <button @click="predict" v-bind:class="{disabled: !can_predict}" class="btn btn-success">
            Predict
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  // http://www.williammalone.com/articles/create-html5-canvas-javascript-drawing-app/
  export default {
    name: "Canvas",
    props: [
      'can_predict'
    ],
    data() {
      return {
        paint: false,
        clickX: [],
        clickY: [],
        clickDrag: [],
        // The following are set in mounted
        context: null,
        offsetX: 0,
        offsetY: 0
      }
    },
    methods: {
      predict: function () {
        this.$emit('predict');
      },
      clear: function (force = false) {
        if (this.can_predict || force) {
          this.context.clearRect(0, 0, this.context.canvas.width, this.context.canvas.height);
          this.clickX = [];
          this.clickY = [];
          this.clickDrag = [];
        }
      },
      mouse_down: function (e) {
        if (this.can_predict) {
          let mouseX = e.clientX - this.offsetX;
          let mouseY = e.clientY - this.offsetY;
          this.paint = true;
          this.addClick(mouseX, mouseY);
          this.redraw();
        }
      },
      mouse_move: function (e) {
        if (this.can_predict) {
          if (this.paint) {
            let mouseX = e.pageX - this.offsetX;
            let mouseY = e.pageY - this.offsetY;
            this.addClick(mouseX, mouseY, true);
            this.redraw();
          }
        }
      },
      mouse_up: function (e) {
        this.paint = false;
      },
      mouse_leave: function (e) {
        this.paint = false;
      },
      addClick: function (x, y, dragging) {
        this.clickX.push(x);
        this.clickY.push(y);
        this.clickDrag.push(dragging);
      },
      redraw: function redraw() {
        let self = this;
        this.context.clearRect(0, 0, this.context.canvas.width, this.context.canvas.height); // Clears the canvas
        this.context.strokeStyle = "#000000";
        this.context.lineJoin = "round";
        this.context.lineWidth = 7;

        for (let i = 0; i < this.clickX.length; i++) {
          self.context.beginPath();
          if (self.clickDrag[i] && i) {
            self.context.moveTo(self.clickX[i - 1], self.clickY[i - 1]);
          } else {
            self.context.moveTo(self.clickX[i] - 1, self.clickY[i]);
          }
          self.context.lineTo(self.clickX[i], self.clickY[i]);
          self.context.closePath();
          self.context.stroke();
        }
      }
    },
    mounted() {
      this.context = document.getElementById('drawing-board').getContext("2d");
      // Set correct width & height
      this.context.canvas.width = 200;
      this.context.canvas.height = 200;
      // Set offset
      let rect = document.getElementById('drawing-board').getBoundingClientRect();
      this.offsetX = rect.left;
      this.offsetY = rect.top;
    }
  }
</script>

<style lang="scss" scoped>
  .input {
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    .canvas-container {
      display: flex;
      justify-content: center;

      #drawing-board {
        width: 200px;
        height: 200px;
        border: 1px solid black;
      }

    }

    .button-container {
      margin-top: 20px;
      display: flex;
      justify-content: flex-end;

      .btn {
        margin-left: 20px;
        box-shadow: none;
      }

    }
  }
</style>
