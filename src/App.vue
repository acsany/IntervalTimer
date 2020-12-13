<template>
  <div class="container">
        <header>
            <h1>Interval Timer</h1>
            <p>A simple timer. Perfect for workouts or baking pancakes.</p>
        </header>
        <main>
            <form>
                <fieldset class="ui-border">
                    <legend>Sets</legend>
                    <input
                        name="repetitions"
                        v-model.number="repetitions"
                        type="number"
                        min="1"
                        max="99"
                        class="ui-border"
                    >
                    <label class="right" for="repetitions">×</label>
                </fieldset>
                <fieldset class="ui-border">
                    <legend>Interval</legend>
                    <input
                        name="intervalDuration"
                        v-model.number="intervalSeconds"
                        type="number"
                        min="1"
                        max="999"
                        class="ui-border"
                    >
                    <label class="right" for="intervalDuration">seconds</label>
                </fieldset>
            </form>
            <p>
                <small>Total duration: {{durationSeconds}}s</small>
            </p>
        </main>
        <section>
            <button
                v-if="!timerActive && totalTimerCount == 0"
                @click="startTimer"
                class="ui-border"
            >
                Start {{repetitions}}×{{intervalSeconds}}s  Timer
            </button>
            <template v-if="totalTimerCount > 0">
                <button @click="resetTimer" class="ui-border">
                    Reset Timer
                </button>
                <button v-if="timerActive" @click="toggleTimer" class="ui-border">
                    Pause Timer
                </button>
                <button v-if="!timerActive" @click="toggleTimer" class="ui-border">
                    Resume Timer
                </button>

            </template>
        </section>
        <section
            class="timer"
            v-show="timerActive"
        >
            <p class="ui-center">{{activeInterval}} of {{repetitions}} Sets</p>
            <p class="ui-super-jumbo">
                {{intervalTimerCount}}s
            </p>
            <p class="ui-jumbo">{{totalTimerCount}}/{{durationSeconds}}s</p>
            <p class="ui-center">
                <template v-for="repetition in repetitions"  v-bind:key="repetition.id">
                    <span v-if="repetition == activeInterval">✹</span>
                    <span v-else>◉</span>
                </template>
            </p>

            <template v-if="totalTimerCount > 0">
                <button @click="resetTimer" class="ui-border">
                    Reset Timer
                </button>
                <button v-if="timerActive" @click="toggleTimer" class="ui-border">
                    Pause Timer
                </button>
                <button v-if="!timerActive" @click="toggleTimer" class="ui-border">
                    Resume Timer
                </button>

            </template>
        </section>
        <section class="timer" v-show="timerComplete">

            <p class="ui-jumbo">
                🎉<br>
                {{repetitions}}/{{repetitions}} sets in {{durationSeconds}} seconds done!
            </p>

            <button @click="resetTimer;timerComplete = !timerComplete" class="ui-border">
                Reset Timer
            </button>
        </section>
    </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
        repetitions: 4,
        intervalSeconds: 2,
        timerActive: false,
        currentInterval: 0,
        totalTimerCount: 0,
        intervalTimerCount: 0,
        timerComplete: false,
        intervalID: undefined
    }
  },
  computed: {
        durationSeconds() {
            return this.repetitions * this.intervalSeconds
        },
        durationMins() {
            return this.durationSeconds / 60
        },
        activeInterval() {
            if (this.timerActive && this.totalTimerCount > 0) {
                return this.currentInterval
            } else {
                return 0
            }
        }
    },
    methods: {
        startTimer() {
            console.log("Starting Timer ...")
            this.timerComplete = false
            this.timerActive = true
            this.totalTimerCount = this.durationSeconds
            this.newInterval()
            this.countdown()
        },
        resetTimer() {
            console.log("... Timer reset.")
            this.timerActive = false
            this.currentInterval = 0
            this.totalTimerCount = 0
            this.intervalTimerCount = 0
            clearInterval(this.intervalID)
        },
        toggleTimer() {
            this.timerActive = !this.timerActive
            if (!this.timerActive) {
                clearInterval(this.intervalID)
            } else {
                this.countdown()
            }
        },
        newInterval() {
            this.intervalTimerCount = this.intervalSeconds
            this.currentInterval++
            new Audio(require("@/assets/notification_high-intensity.wav")).play();
        },
        countdown() {
            if (this.totalTimerCount > 0 && this.timerActive) {
                this.intervalID = setInterval(() => {
                    this.totalTimerCount--
                    this.intervalTimerCount--

                    if (this.totalTimerCount == 0) {
                        this.resetTimer()
                        this.timerComplete = true
                        // Doubled for simple increase of volume
                        new Audio(require("@/assets/hero_decorative-celebration-01.wav")).play();
                        new Audio(require("@/assets/hero_decorative-celebration-01.wav")).play();

                    }

                    if (this.intervalTimerCount == 0 && !this.timerComplete) {
                        this.newInterval()
                    }
                }, 1000);
            }
        }
    }
}
</script>

<style>
  @import './assets/style.css';
</style>
