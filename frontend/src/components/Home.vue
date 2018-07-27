<template>
  <div>
    <div class="row d-flex">
      <div class="col-lg-6 col-m-12 mb-4">
        <div class="card bg-default">
          <div class="card-header font-weight-bold">Input</div>
          <div class="card-body input">
            <div class="canvas-container">
              <canvas id="the_stage">Canvas</canvas>
            </div>
            <div class="button-container">
              <button v-bind:class="{disabled: !can_predict}" class="btn btn-secondary">
                Clear
              </button>
              <button @click="predict" v-bind:class="{disabled: !can_predict}" class="btn btn-success">
                Predict
              </button>
            </div>
          </div>
        </div>
      </div>

      <div class="col-lg-6 col-m-12 mb-4">
        <div class="card bg-default">
          <div class="card-header font-weight-bold">Output</div>
          <div class="card-body output">
            <div id="not-started">
              <div class="centerer">
                <div>
                  <img class="mascot" src="../assets/dog.png">
                </div>
                <div class="mascot_do_draw">
                  <div class="speech-bubble">
                    Hi!<br>
                    Draw a single digit from 0-9 in the previous input box, and I'll do my best
                    to guess what you drew!
                  </div>
                </div>
              </div>
            </div>
            <div id="thinking">
              <div class="row">
                <img class="mascot" src="../assets/dog_think.png">
                <div class="speech-bubble">
                  Im thinking!
                </div>
              </div>
              <div class="bubblingG">
                <span id="bubblingG_1"></span>
                <span id="bubblingG_2"></span>
                <span id="bubblingG_3"></span>
              </div>
            </div>
            <div id="predictions">
              <div class="centerer">
                <img class="mascot" src="../assets/kitty_predictions.png">
                <div class="speech-bubble">
                  <h2>This is my prediction</h2>
                  <span>5</span>
                  Was I correct?
                </div>
              </div>
              <div class="button-container">
                <button class="btn btn-default">Try Again</button>
                <button class="btn btn-danger">Not Correct</button>
                <button class="btn btn-success">Correct</button>
              </div>
            </div>

            <div id="incorrect">
              <div class="centerer">
                <img class="mascot" src="../assets/kitty_sad.png">
                <div class="speech-bubble">
                  Can you tell me what digit you drew, so that I can
                  learn from you?
                </div>
              </div>
              <div class="button-container">
                <div class="btn-group" role="group" aria-label="Basic example">
                  <button type="button" class="btn btn-secondary">0</button>
                  <button type="button" class="btn btn-secondary">1</button>
                  <button type="button" class="btn btn-secondary">2</button>
                  <button type="button" class="btn btn-secondary">3</button>
                  <button type="button" class="btn btn-secondary">4</button>
                  <button type="button" class="btn btn-secondary">5</button>
                  <button type="button" class="btn btn-secondary">6</button>
                  <button type="button" class="btn btn-secondary">7</button>
                  <button type="button" class="btn btn-secondary">8</button>
                  <button type="button" class="btn btn-secondary">9</button>
                  <button type="button" class="btn btn-danger">No</button>

                </div>
              </div>
            </div>

            <div id="improve">
              <div class="centerer">
                <img class="mascot" src="../assets/kitty_train.png">
                <div class="speech-bubble">
                  Thanks! I will use that information to improve my future predictions!
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Home",
    data() {
      return {
        can_predict: true,
        // Output Stages are set in the mounted section.
        output_stages: {}
      }
    },
    methods: {
      clear: function () {

      },
      predict: function () {
        if (this.can_predict) {
          this.can_predict = false;
          this.set_output_stage(this.output_stages.thinking)
        }
      },
      set_output_stage: async function (stage) {
        await this.hide_all();
        await new Promise(resolve => setTimeout(resolve, 500));
        if (stage === this.output_stages.start) {
          this.output_stages.start.style.maxHeight = "390px";
        }
        else if (stage === this.output_stages.thinking) {
          this.output_stages.thinking.style.maxHeight = "390px";
        }
        else if (stage === this.output_stages.prediction) {
          this.output_stages.prediction.style.maxHeight = "390px";
        }
        else if (stage === this.output_stages.incorrect) {
          this.output_stages.incorrect.style.maxHeight = "390px";
        }
        else if (stage === this.output_stages.training) {
          this.output_stages.training.style.maxHeight = "390px";
        }
      },
      hide_all: async function () {
        this.output_stages.start.style.maxHeight = 0;
        this.output_stages.thinking.style.maxHeight = 0;
        this.output_stages.prediction.style.maxHeight = 0;
        this.output_stages.incorrect.style.maxHeight = 0;
        this.output_stages.training.style.maxHeight = 0;
      }
    },
    mounted() {
      this.$store.commit('change_component', {name: this.$options.name});
      this.output_stages = {
        start: document.getElementById('not-started'),
        thinking: document.getElementById('thinking'),
        prediction: document.getElementById('predictions'),
        incorrect: document.getElementById('incorrect'),
        training: document.getElementById('improve')
      }
    }
  }
</script>

<style lang="scss" scoped>
  .card {
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    height: 100%;

    * {
      transition: 2s all;
    }

    .input {
      display: flex;
      flex-direction: column;
      justify-content: space-between;

      .canvas-container {
        display: flex;
        justify-content: center;

        #the_stage {
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
        }
      }
    }
    .output {
      height: 390px;

      img {
        width: 150px!important;
        height: 150px!important;
        margin-right: 20px;
      }

      #not-started {
        display: flex;
        height: 100%;
        max-height: 390px;
        flex-direction: row;
        justify-content: center;
        overflow: hidden;
        .centerer {
          display: flex;
          align-items: center;

        }
      }

      #thinking {
        display: flex;
        flex-direction: column;
        height: 100%;
        max-height: 0;
        overflow: hidden;
        .row {
          justify-content: center;
        }
      }
    }

    #predictions {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      height: 100%;
      max-height: 0;
      overflow: hidden;
      .centerer {
        display: flex;
        .speech-bubble {
          height: fit-content;
          text-align: center;
          h2 {
            font-size: 18px;
            font-weight: bold;
          }
          span {
            display: block;
            font-size: 45px;
          }
        }
      }
    }

    #incorrect {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      height: 100%;
      max-height: 0;
      overflow: hidden;
      .centerer {
        display: flex;
        .speech-bubble {
        }
      }
      .btn-group {
        width: 100%;
        height: auto;
        display: block;
        text-align: center;
        button {
          border-radius: 5px !important;
          margin: 10px !important;
          font-size: 22px;
          width: 75px;
        }
      }
    }

    #improve {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      height: 100%;
      max-height: 0;
      overflow: hidden;
      .centerer {
        display: flex;
        .speech-bubble {
        }
      }
    }

    .speech-bubble {
      position: relative;
      padding: 20px;
      height: 100%;
      margin-left: 20px;
      background: #ececec;
      border-radius: .4em;
    }

    .speech-bubble:after {
      content: '';
      position: absolute;
      left: 0;
      top: 50%;
      width: 0;
      height: 0;
      border: 24px solid transparent;
      border-right-color: #ececec;
      border-left: 0;
      border-bottom: 0;
      margin-top: -12px;
      margin-left: -24px;
    }

    .bubblingG {
      padding-top: 20px;
      text-align: center;
      width: 78px;
      height: 49px;
      margin: auto;
    }

    .bubblingG span {
      display: inline-block;
      vertical-align: middle;
      width: 10px;
      height: 10px;
      margin: 24px auto;
      background: rgb(0, 0, 0);
      border-radius: 49px;
      -o-border-radius: 49px;
      -ms-border-radius: 49px;
      -webkit-border-radius: 49px;
      -moz-border-radius: 49px;
      animation: bubblingG 1.5s infinite alternate;
      -o-animation: bubblingG 1.5s infinite alternate;
      -ms-animation: bubblingG 1.5s infinite alternate;
      -webkit-animation: bubblingG 1.5s infinite alternate;
      -moz-animation: bubblingG 1.5s infinite alternate;
    }

    #bubblingG_1 {
      animation-delay: 0s;
      -o-animation-delay: 0s;
      -ms-animation-delay: 0s;
      -webkit-animation-delay: 0s;
      -moz-animation-delay: 0s;
    }

    #bubblingG_2 {
      animation-delay: 0.45s;
      -o-animation-delay: 0.45s;
      -ms-animation-delay: 0.45s;
      -webkit-animation-delay: 0.45s;
      -moz-animation-delay: 0.45s;
    }

    #bubblingG_3 {
      animation-delay: 0.9s;
      -o-animation-delay: 0.9s;
      -ms-animation-delay: 0.9s;
      -webkit-animation-delay: 0.9s;
      -moz-animation-delay: 0.9s;
    }

    @keyframes bubblingG {
      0% {
        width: 10px;
        height: 10px;
        background-color: rgb(0, 0, 0);
        transform: translateY(0);
      }

      100% {
        width: 23px;
        height: 23px;
        background-color: rgb(255, 255, 255);
        transform: translateY(-20px);
      }
    }

    @-o-keyframes bubblingG {
      0% {
        width: 10px;
        height: 10px;
        background-color: rgb(0, 0, 0);
        -o-transform: translateY(0);
      }

      100% {
        width: 23px;
        height: 23px;
        background-color: rgb(255, 255, 255);
        -o-transform: translateY(-20px);
      }
    }

    @-ms-keyframes bubblingG {
      0% {
        width: 10px;
        height: 10px;
        background-color: rgb(0, 0, 0);
        -ms-transform: translateY(0);
      }

      100% {
        width: 23px;
        height: 23px;
        background-color: rgb(255, 255, 255);
        -ms-transform: translateY(-20px);
      }
    }

    @-webkit-keyframes bubblingG {
      0% {
        width: 10px;
        height: 10px;
        background-color: rgb(0, 0, 0);
        -webkit-transform: translateY(0);
      }

      100% {
        width: 23px;
        height: 23px;
        background-color: rgb(255, 255, 255);
        -webkit-transform: translateY(-20px);
      }
    }

    @-moz-keyframes bubblingG {
      0% {
        width: 10px;
        height: 10px;
        background-color: rgb(0, 0, 0);
        -moz-transform: translateY(0);
      }

      100% {
        width: 23px;
        height: 23px;
        background-color: rgb(255, 255, 255);
        -moz-transform: translateY(-20px);
      }
    }
  }
</style>
