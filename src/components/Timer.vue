<template>
  <div>
    <h1>{{ remaining }}</h1>
    <label>remaining in round</label>
    
    <div>
      <button
        v-if="isRunning"
        title="Pause"
        @click="pause(true)"
      >Emergency Pause!</button>
      <button
        v-else
        title="Resume"
        @click="pause(false)"
      >Resume timer!</button>
    </div>
  </div>
</template>
<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import { Prop, Watch } from 'vue-property-decorator'

@Options({
  name: 'Timer',
})
export default class Timer extends Vue {
  @Prop({default: 1, type: Number})
  private initial!: number; // Minutes

  public isRunning = true;

  private timeRemaining = 0; // Seconds

  public get remaining(): string{
    const minutes = Math.floor(this.timeRemaining / 60).toString().padStart(2, '0');
    const seconds = (this.timeRemaining % 60).toString().padStart(2, '0');
    return `${minutes}:${seconds}`;
  }

  public pause(isPause: boolean): void {
    this.isRunning = !isPause;
  }

  @Watch("isRunning")
  private async isRunningWatcher(value: boolean): Promise<void> {
    if (!value){
      return;
    }
    this.countdown();
  }

  @Watch("timeRemaining")
  private async timeRemainingWatcher(value: number): Promise<void> {
    if (value <= 0 || !this.isRunning){
      return;
    }
    this.countdown();
  }

  private countdown(): void {
    new Promise(r => {
      setTimeout(() => {
        if (this.isRunning) {
          this.timeRemaining--;
        }
      }, 1000);
    });
  }

  public mounted() {
     this.timeRemaining = this.initial * 60;
  }
}
</script>
<style scoped>
  h1 {
    margin-top: -40px;
    margin-bottom: -40px;
    font-size: 40vh;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    display: inline-block;
    margin: 0 10px;
  }
  a {
    color: #42b983;
  }
</style>
