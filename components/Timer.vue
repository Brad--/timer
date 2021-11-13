<template>
  <div class="container">
    <div class="input-group">
      <input v-model.number="hours" :class="hoursClass" />h
      <input v-model.number="minutes" :class="minutesClass" />m
      <input v-model.number="seconds" :class="secondsClass" />s
      
      <transition name="button-fade" mode="out-in">
        <div v-if="!started" class="button-group">
          <button @click="start">Start</button>
          <button v-if="alarm && !alarm.ended" class="small-button" @click="stopAlarm">Alarm</button>
        </div>
        <div v-else class="button-group">
          <button @click="pauseToggle">{{ paused ? 'Resume' : 'Pause' }}</button>
          <button class="small-button" @click="reset">Reset</button>
          <button class="small-button" @click="deleteIt">Delete</button>
        </div>
      </transition>
    </div>

    <Crescent />
  </div>
</template>

<script>
const defaultTimer = { h: 0, m: 0, s: 0 }

// Make the input bigger for 3 digits
const widen = (num) => {
  return { 'wide-input': num > 99}
}

export default {
  data() {
    return {
      hours: 0,
      minutes: 0,
      seconds: 0,
      settings: { ...defaultTimer },
      started: false,
      paused: false,
      timer: null,
      alarm: null
    }
  },
  computed: {
    hoursClass() {
      return widen(this.hours)
    },
    minutesClass() {
      return widen(this.minutes)
    },
    secondsClass() {
      return widen(this.seconds)
    },
    currentSetting() {
      return {h: this.hours, m: this.minutes, s: this.seconds}
    },
    isDone () {
      return this.hours === 0 && this.minutes === 0 && this.seconds === 0
    }
  },
  methods: {
    start () {
      this.fixTimer()
      if (this.isDone) return

      this.settings = this.currentSetting
      this.started = true
      
      this.timer = setInterval(this.runTimer, 1000)
    },
    pauseToggle () {
      this.paused = !this.paused
    },
    reset () {
      this.paused = true
      this.resetTimer()
    },
    deleteIt () {
      this.started = false
      this.paused  = false
      this.settings = { ...defaultTimer }
      this.clearTimer()
      this.resetTimer()
    },
    resetTimer () {
      this.hours   = this.settings.h
      this.minutes = this.settings.m
      this.seconds = this.settings.s
    },
    fixTimer() {
      if (!this.hours)   this.hours   = 0
      if (!this.minutes) this.minutes = 0
      if (!this.seconds) this.seconds = 0
    },
    runTimer () {
      this.fixTimer()
      if (!this.paused) {
        if (this.seconds === 0) {
          if (this.minutes === 0) {
            this.hours -= 1
            this.minutes = 59
          } else {
            this.minutes -= 1
            this.seconds = 59
          }
        } else {
          this.seconds -= 1
        }
      }
      if (this.isDone) {
        this.done()
      }
    },
    done () {
      this.started = false
      this.clearTimer()

      this.playAlarm()
    },
    async playAlarm () {
      this.alarm = new Audio(require('@/assets/alarmtone.mp3').default)
      this.alarm.play()
      setTimeout(() => { this.stopAlarm() }, 10000)
    },
    stopAlarm () {
      if (this.alarm) {
        this.alarm.pause()
        this.alarm = null
      }
    },
    clearTimer () {
      if (this.timer) {
        clearInterval(this.timer)
      }
    }
  },
}
</script>

<style scoped>
.container {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 70%;
}

.input-group {
  position: absolute;
  font-size: 8rem;

  z-index: 5;
}

.button-group {
  display: flex;
  flex-direction: row;
  justify-content: center;
  padding-top: 2rem;
}

button {
  font-size: 4rem;
  background: none;
  border: none;
}

.small-button {
  font-size: 2rem;
  margin-right: .25rem;
  margin-left: .25rem;
}

button:hover {
  box-shadow: 0 .125rem 0 black;
}

.wide-input {
  width: 15rem;
}

input {
  font-size: 8rem;
  width: 10rem;
  border: none;
  background-color: inherit;
  border-bottom: .25rem solid black;
}

input:focus-visible {
  outline: none;
  border: none;
  background-color: inherit;
  border-bottom: .25rem solid black;
}
</style>