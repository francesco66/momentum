<template>

	<div class="consolas cover tc bg-light-purple flex flex-column items-center pa2">
		<h1 class="f1 dark-red pa2">MoMentum</h1>
    <box-data :dati="dati"/>
    <a class="f5 link dim ba bw2 br3 b--red br3 ph3 pv2 mt3 mb2 dib white bg-black" @click="aggiorna()">Aggiorna</a>

    <footer>
      <p class="mt4 mt5-l"><strong class="dark-red">MoMentum</strong> è un'idea geniale di FRA</p>
    </footer>
  </div>

</template>

<script>
import { getSunrise, getSunset } from 'sunrise-sunset-js';
import boxData from '~/components/boxData.vue';

function secondiToOreMinutiSecondi(durata) {
	let ore = Math.floor(durata/3600);
	let minuti = Math.floor((durata - ore*3600)/60);
	let secondi = durata - ore*3600 - minuti*60;
	return [ ore, minuti, secondi ]
}

function durataToSecondi(ore, minuti, secondi) {
	return secondi + minuti*60 + ore*3600
}

// Rapallo secondo geolocalizzazione
let Latitudine = 44.3538;
let Longitudine = 9.2436;

function getDati(Latitudine, Longitudine) {

  let dati = new Object;
  dati.Longitudine = Longitudine;
  dati.Latitudine = Latitudine;

  /* Sunset and Sunrise tonight at the Triggertrap office for today */
  const sunrise = getSunrise(Latitudine, Longitudine);
  const sunset = getSunset(Latitudine, Longitudine);

  // converting the string to an array
  let subAlba = sunrise.toString().split(' ');
  let subTramonto = sunset.toString().split(' ');
  dati.alba = subAlba[4];
  dati.tramonto = subTramonto[4];

  // la durata del giorno solare sarà tramonto - alba ...
  let albaArray = dati.alba.split(':');
  let tramontoArray = dati.tramonto.split(':');
  let albaOre = Number(albaArray[0]);
  let albaMinuti = Number(albaArray[1]);
  let albaSecondi = Number(albaArray[2]);
  let tramontoOre = Number(tramontoArray[0]);
  let tramontoMinuti = Number(tramontoArray[1]);
  let tramontoSecondi = Number(tramontoArray[2]);

  let giornoSolareSecondi = durataToSecondi(tramontoOre, tramontoMinuti, tramontoSecondi) - durataToSecondi(albaOre, albaMinuti, albaSecondi);
  let giornoSolareA = secondiToOreMinutiSecondi(giornoSolareSecondi);
  dati.giornoSolareS = giornoSolareA[0] + ":" + giornoSolareA[1] + ":" + giornoSolareA[2];
  let momentumA = secondiToOreMinutiSecondi((giornoSolareSecondi/12)/40);
  dati.momentumS = momentumA[1] + " minuto e " + Math.round(Number(momentumA[2])) + " secondi";

  const oggiMS = Date.now();
  let dataInArray = new Intl.DateTimeFormat('it-IT', { dateStyle: 'full', timeStyle: 'long' }).formatToParts(oggiMS);
  dati.oggi = dataInArray[0].value + " " + dataInArray[2].value + " " + dataInArray[4].value + " " + dataInArray[6].value; 
  dati.orario = dataInArray[8].value + dataInArray[9].value + dataInArray[10].value + dataInArray[11].value + dataInArray[12].value; 

  return dati;
}

export default {

  components: { boxData },

  data() {
    let dati = getDati(Latitudine, Longitudine);
    return {
      dati: dati,
    }
  },

  methods: {
    async getLocation() {
      return new Promise((resolve, reject) => {
        if(!("geolocation" in navigator)) {
          reject(new Error('Geolocation is not available.'));
        }
        navigator.geolocation.getCurrentPosition(pos => {
          resolve(pos);
        }, err => {
          reject(err);
        });
      });
    },

    async aggiorna() {
      try {
        let location = await this.getLocation();
        this.Latitudine = location.coords.latitude;
        this.Longitudine = location.coords.longitude;
        this.dati = getDati(this.Latitudine, this.Longitudine);
      } catch(e) {
        console.log(e.message);
      }
    }
  }

}
</script>
