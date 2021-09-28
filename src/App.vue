<template>
  <div id="app">
    <div class="container-fluid">
      <div class="row justify-content-center">
        <div
          class="
            col-11 col-sm-9 col-md-7 col-lg-6 col-xl-10
            text-center
            p-0
            mt-3
            mb-2
          "
        >
          <div class="card px-0 pt-4 pb-0 mt-3 mb-3">
            <h2 id="heading">Progressbar</h2>
            <!-- progressbar -->
            <ul id="progressbar">
              <li id="account" v-for="step in progress" :key="step.id">
                <div>
                  <div
                    class="progress-step"
                    :class="{
                      active: step.showActive,
                      'half-active':
                        step.halfComplete && activeState === step.id,
                    }"
                  >
                    <span class="step-no">{{ step.id }}</span>
                  </div>
                  <div class="d-flex align-items-center">
                    <div class="active-dot" v-if="activeState === step.id" />
                    <strong>{{ step.name }}</strong>
                  </div>
                </div>
                <div
                  class="half-complete-status"
                  v-if="step.halfComplete && activeState === step.id"
                >
                  <p>50% Completed</p>
                </div>
                <div
                  class="progress"
                  :style="{
                    background: putBgColor(step, activeState),
                  }"
                >
                  <div
                    class="
                      progress-bar progress-bar-striped progress-bar-animated
                    "
                    role="progressbar"
                    aria-valuemin="0"
                    aria-valuemax="100"
                    :style="{
                      width: putWidth(step, activeState),
                    }"
                  ></div>
                </div>
              </li>
            </ul>
            <br />
            <div>
              <button
                type="button"
                class="btn action-button previous"
                v-if="showPrevBtn"
                @click="previous"
              >
                Previous
              </button>
              <button
                type="button"
                class="btn action-button next"
                @click="next"
                v-if="showNextBtn"
              >
                Next
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      progress: [
        {
          id: 1,
          name: "Your Prefereneces",
          halfComplete: false,
          complete: false,
          showActive: true,
        },
        {
          id: 2,
          name: "Personal Information",
          halfComplete: false,
          complete: false,
          showActive: false,
        },
        {
          id: 3,
          name: "Family Information",
          halfComplete: false,
          complete: false,
          showActive: false,
        },
        {
          id: 4,
          name: "Image Upload",
          halfComplete: false,
          complete: false,
          showActive: false,
        },
      ],
      activeState: 1,
    };
  },
  computed: {
    showNextBtn() {
      return this.activeState <= this.progress.length - 1;
    },
    showPrevBtn() {
      return this.progress[0].halfComplete;
    },
  },

  methods: {
    getStepState(arr, state, prop) {
      return arr.find((item) => item.id === state)[prop];
    },

    changeStepToHalfCompleteForNext(index, halfState) {
      const newObj = {
        ...this.progress[index],
        halfComplete: halfState,
      };
      this.progress.splice(index, 1, newObj);
    },
    changeStepToHalfCompleteForPrevious(index, halfState) {
      const newObj = {
        ...this.progress[index],
        halfComplete: halfState,
      };
      this.progress.splice(index, 1, newObj);
    },

    next() {
      const halfComplete = this.getStepState(
        this.progress,
        this.activeState,
        "halfComplete"
      );
      const fullComplete = this.getStepState(
        this.progress,
        this.activeState,
        "fullComplete"
      );

      if (!halfComplete && !fullComplete) {
        this.changeStepToHalfCompleteForNext(this.activeState - 1, true);
      }
      if (halfComplete && !fullComplete) {
        const index = this.activeState - 1;
        const newObj = {
          ...this.progress[index],
          complete: true,
        };
        const nextObj = {
          ...this.progress[index + 1],
          showActive: true,
        };
        this.progress.splice(index, 1, newObj);
        this.progress.splice(index + 1, 1, nextObj);
        setTimeout(() => {
          if (this.activeState < this.progress.length) {
            this.activeState = this.activeState + 1;
          }
        }, 0);
      }
    },
    previous() {
      const halfComplete = this.getStepState(
        this.progress,
        this.activeState,
        "halfComplete"
      );
      const fullComplete = this.getStepState(
        this.progress,
        this.activeState,
        "fullComplete"
      );
      if (halfComplete && !fullComplete) {
        this.changeStepToHalfCompleteForPrevious(this.activeState - 1, false);
      }
      if (!halfComplete && !fullComplete) {
        const index = this.activeState - 1;
        const newObj = {
          ...this.progress[index],
          showActive: false,
        };
        const prevObj = {
          ...this.progress[index - 1],
          halfComplete: false,
          complete: false,
        };
        this.progress.splice(index, 1, newObj);
        this.progress.splice(index - 1, 1, prevObj);
        setTimeout(() => {
          this.activeState = this.activeState - 1;
        }, 0);
      }
    },
    putWidth(step, active) {
      if (step.halfComplete && active === step.id && !step.complete) {
        return "50%";
      } else if (step.complete && active === step.id && step.halfComplete) {
        return "100%";
      }
    },
    putBgColor(step, active) {
      if (step.complete && active === step.id && step.halfComplete) {
        return "#673ab7";
      } else if (step.complete && active > step.id && step.halfComplete) {
        return "#673ab7";
      } else {
        return "lightgray";
      }
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
* {
  margin: 0;
  padding: 0;
}

html {
  height: 100%;
}

p {
  color: grey;
}

#heading {
  text-transform: uppercase;
  color: #673ab7;
  font-weight: normal;
  padding-bottom: 20px;
}

.card {
  z-index: 0;
  border: none;
  position: relative;
}

.fs-title {
  font-size: 25px;
  color: #673ab7;
  margin-bottom: 15px;
  font-weight: normal;
  text-align: left;
}

.purple-text {
  color: #673ab7;
  font-weight: normal;
}

.steps {
  font-size: 25px;
  color: gray;
  margin-bottom: 10px;
  font-weight: normal;
  text-align: right;
}

.fieldlabels {
  color: gray;
  text-align: left;
}

#progressbar {
  margin-bottom: 30px;
  overflow: hidden;
  color: lightgrey;
  margin-left: 15%;
}

#progressbar li {
  list-style-type: none;
  font-size: 15px;
  width: 25%;
  float: left;
  position: relative;
  font-weight: 400;
}

.progress {
  height: 10px;
  position: absolute;
  top: 30%;
  left: 2%;
  width: 100%;
  z-index: -1;
}

.progress-bar {
  background-color: #673ab7;
}

.fit-image {
  width: 100%;
  object-fit: cover;
}

.progress-step {
  width: 50px;
  height: 50px;
  line-height: 45px;
  display: block;
  font-size: 20px;
  color: #ffffff;
  background: lightgray;
  border-radius: 50%;
  margin: 0;
  padding: 2px;
  z-index: 99;
}
.progress-step.active,
.progress-step.half-active {
  background: #673ab7;
}
#progressbar li:last-child .progress,
#progressbar li:last-child .half-complete-status {
  display: none;
}
.half-complete-status {
  height: 18px;
  position: absolute;
  top: 0%;
  left: 35%;
  width: 50%;
  background-color: rgba(103, 58, 183, 0.3);
}
.half-complete-status p {
  margin-bottom: 0;
  font-size: 12px;
  color: #673ab7;
  font-weight: 600;
}
#account strong {
  display: flex;
  color: #673ab7;
  margin-top: 10px;
}
.active-dot {
  width: 8px;
  height: 8px;
  background: orange;
  border-radius: 50%;
  margin-top: 10px;
  margin-right: 5px;
}
.action-button {
  width: 100px;
  font-weight: bold;
  color: white;
  border: 0 none;
  border-radius: 0px;
  cursor: pointer;
  padding: 10px 5px;
  margin: 10px 0px 10px 5px;
}
.next {
  background: #673ab7;
}
.previous {
  background: black;
}
.step-no {
  color: white;
  font-size: 25px;
  font-weight: 600;
}
</style>
