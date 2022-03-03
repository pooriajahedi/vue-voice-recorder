<script>
import Recorder from 'js-audio-recorder';
import moment from 'moment';

export default {
  name: "VoiceRecorder",
  data() {
    return {
      recorder: null,
      is_recording: false,
      is_pause: false,
      recorder_timer: 0
    }
  },
  methods: {
    startRecord() {
      this.recorder.start().then(function () {
        this.is_recording = true;
        this.HandleRecorderTimer;
      }.bind(this));
    },
    StopRecord() {
      this.is_recording = false;
      this.recorder.stop();
      this.recorder.stopPlay();
      this.recorder.downloadWAV('myrecord');
    },
    PauseRecord() {
      this.recorder.pause();
      this.is_pause = true;
      console.log('pause');
    },
    ResumeRecord() {
      this.recorder.resume();
      this.is_pause = false;
      console.log('resume');
    }

  },
  watch: {},
  computed: {
    HandleRecorderTimer() {
      this.recorder.onprogress = function (params) {
        this.recorder_timer = moment().startOf('day')
            .seconds(params.duration)
            .format('H:mm:ss');
      }.bind(this);
    }
  },
  mounted() {
    this.recorder = new Recorder();
  }
}
</script>

<template>
  <div id="recorder-wrapper">
    <button class="start" title="start" type="button" @click="startRecord" v-show="!is_recording"><i
        class="fas fa-microphone"></i></button>
    <button class="resume" title="resume" type="button" @click="ResumeRecord" v-show="is_recording && is_pause"><i
        class="fas fa-play"></i></button>
    <button class="pause" title="pause" type="button" @click="PauseRecord" v-show="is_recording && !is_pause"><i
        class="fas fa-pause"></i></button>
    <button class="stop" title="stop" type="button" @click="StopRecord" v-show="is_recording"><i
        class="fas fa-stop"></i></button>
    <span v-show="is_recording" id="timer-recorder" v-html="this.recorder_timer"></span>
  </div>
</template>

<style scoped>
#recorder-wrapper {
  position: fixed;
  bottom: 10px;
  left: 10px;
}

#timer-recorder {
  background-color: #33d9b2;
  border-radius: 10px;
  padding: 2px 8px;
  margin-left: 5px;
}

#recorder-wrapper button.start {
  background-color: red;
  border: none;
  color: #fff;
  height: 50px;
  width: 50px;
  font-size: 25px;
  border-radius: 50px;
  box-shadow: 0px 0px 5px #6e0a0a;
  cursor: pointer;
}

#recorder-wrapper button.pause,
#recorder-wrapper button.resume,
#recorder-wrapper button.stop {
  background-color: #ddd;
  border: none;
  color: #fff;
  height: 30px;
  width: 30px;
  font-size: 15px;
  border-radius: 50px;
  cursor: pointer;
  margin-right: 5px;
}

#recorder-wrapper button.stop {
  background-color: #000;
  position: absolute;
  left: 0;
  bottom: 40px;
}

#recorder-wrapper button.pause,
#recorder-wrapper button.resume {
  background: rgba(51, 217, 178, 1);
  box-shadow: 0 0 0 0 rgba(51, 217, 178, 1);
}

#recorder-wrapper button.pause {
  animation: pulse-green 2s infinite;
}

@keyframes pulse-green {
  0% {
    transform: scale(0.95);
    box-shadow: 0 0 0 0 rgba(51, 217, 178, 0.7);
  }

  70% {
    transform: scale(1);
    box-shadow: 0 0 0 10px rgba(51, 217, 178, 0);
  }

  100% {
    transform: scale(0.95);
    box-shadow: 0 0 0 0 rgba(51, 217, 178, 0);
  }
}

</style>
