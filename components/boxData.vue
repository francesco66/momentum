<template>

	<div class="tc flex flex-column items-center pa2">
    <div class="f5 f4-l outline ba bw2 br3 b--blue w-100 pa2 ma2 white-80 bg-black">
			<p>oggi è {{ dati.oggi }}</p>
      <p>{{ "e sono le: " + hours + ":" + minutes + ":" + seconds }}</p>
    </div>
    <div class="f5 f4-l outline ba bw2 br3 b--blue w-100 pa2 ma2 white-80 bg-black">
 			<p>L'alba sarà alle {{ dati.alba }}</p>
    </div>
    <div class="f5 f4-l outline ba bw2 br3 b--blue w-100 pa2 ma2 white-80 bg-black">
 			<p>Il tramonto sarà alle {{ dati.tramonto }}</p>
    </div>
    <div class="f5 f4-l outline ba bw2 br3 b--blue w-100 pa2 ma2 white-80 bg-black">
 			<p>Il giorno solare dura {{ dati.giornoSolareS }}</p>
    </div>
    <div class="f5 f4-l outline ba bw2 br3 b--blue w-100 pa2 ma2 white-80 bg-black">
 			<p>Il momentum è di:</p>
 			<p>{{ dati.momentumS }}</p>
    </div>
  </div>

</template>

<script>
export default {
  props: ['dati'],

  data () {
    return {
      hours: 0,
      minutes: 0,
      seconds: 0
    }
  },
  methods: {
    setTime () {
      setInterval(() => {
        const date = new Date()
        this.hours = date.getHours()
        this.minutes = this.checkSingleDigit(date.getMinutes())
        this.seconds = this.checkSingleDigit(date.getSeconds())
      }, 1000)
    },
    checkSingleDigit (digit) {
      return ('0' + digit).slice(-2)
    }
  },
  mounted () {
    this.setTime()
  }

}
</script>
