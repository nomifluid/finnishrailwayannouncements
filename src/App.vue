<script setup>
import StationButton from "./components/StationButton.vue";
</script>

<template>
  <div id="app">

    <div class="row">
      <div class="column">
        <StationButton name="Myllylä" @add="add" @remove="remove" />
        <StationButton name="Aikava" @add="add" @remove="remove" />
        <StationButton name="Vähämaa" @add="add" @remove="remove" />
      </div>
      <div class="column">
        <StationButton name="Honkala" @add="add" @remove="remove" />
        <StationButton name="Haapasalmi" @add="add" @remove="remove" />
        <StationButton name="Tervapuro" @add="add" @remove="remove" />
        <StationButton name="Jylppy" @add="add" @remove="remove" />
      </div>
      <StationButton name="Lassila" @add="add" @remove="remove" />
      <StationButton name="Järvela" @add="add" @remove="remove" />
      <div class="column">
        <StationButton name="Nurmiainen" @add="add" @remove="remove" />
        <StationButton name="Marjamaa" @add="add" @remove="remove" />
        <StationButton name="Aittola" @add="add" @remove="remove" />
        <StationButton name="Jussila" @add="add" @remove="remove" />
        <StationButton name="Määttä" @add="add" @remove="remove" />
        <StationButton name="Hirvaskoski" @add="add" @remove="remove" />
      </div>
      <StationButton name="Kosula" @add="add" @remove="remove" />
      <StationButton name="Ylömäki" @add="add" @remove="remove" />
      <div class="column">
        <StationButton name="Viherlinna" @add="add" @remove="remove" />
        <StationButton name="Kortespohja" @add="add" @remove="remove" />
        <StationButton name="Ritola" @add="add" @remove="remove" />
        <StationButton name="Hanasoja" @add="add" @remove="remove" />
        <StationButton name="Pihlava" @add="add" @remove="remove" />
      </div>
      <StationButton name="Tuppula" @add="add" @remove="remove" />
      <StationButton name="Kala" @add="add" @remove="remove" />
      <div class="column">
        <StationButton name="Koivulahti" @add="add" @remove="remove" />
        <StationButton name="Viljakanpohja" @add="add" @remove="remove" />
        <StationButton name="Toikola" @add="add" @remove="remove" />
      </div>
    </div>

    {{ followingStations.toString() }}

    <input type="text" name="train" id="train" v-model="trainName">
    <input type="checkbox" name="callingAtAll" id="callingAtAll" v-model="callingAtAll">
    <label for="callingAtAll">Pysähdymme kaikilla asemalla</label>
    <input type="text" name="tracks" id="tracks" v-model="tracks" placeholder="tracks (separate by comma)">

    <p>
      Hyvät matkustajat, tama on {{ trainName }} {{ direction }}. {{ callingAt }} Hyvää matkaa!
      {{ announcedStations }} Hyvät matkustajat, saavumme {{ direction }}. Kiitämme
      kaikkia matkustajia, tervetuloa uudelleen.
    </p>

    <p>
      <span v-for="(ann) in stationAnnouncements" :key="ann">
        {{ ann }} <br />
      </span>
    </p>

    <hr />
    <section>
      <p>
        {{ xml_st }}
        {{ xml_plat_st }} {{ stationAnnouncements[0] }} {{ xml_plat_en }}
        {{ xml_train_st }} Hyvät matkustajat, tama on {{ trainName }} {{ direction }}. {{ callingAt }} Hyvää matkaa!
        <!-- {{ xml_train_en }} -->
      </p>
      <p v-for="n in (stationAnnouncements.length - 1)" :key="n">
        <!-- ! n starts with 1 -->
        <!-- {{ xml_train_st }} --> Seuraava: {{ followingStations[n] }}. {{ xml_train_en }}
        {{ xml_plat_st }} {{ stationAnnouncements[n] }} {{ xml_plat_en }}
        <span v-if="n === stationAnnouncements.length - 1">
          {{ xml_train_st }} Hyvät matkustajat, saavumme {{ direction }}. Kiitämme
          kaikkia matkustajia, tervetuloa uudelleen. {{ xml_train_en }}
        </span>
        <span v-else>
          {{ xml_train_st }} {{ followingStations[n] }}. <!-- {{ xml_train_en }} -->
        </span>
      </p>
      {{ xml_en }}
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      followingStations: [],
      trainName: 'juna',
      callingAtAll: false,
      directionDict: {
        'Myllylä': 'Myllylään',
        'Vähämaa': 'Vähämaalle',
        'Honkala': 'Honkalaan',
        'Hirvaskoski': 'Hirvaskoskelle',
        'Pihlava': 'Pihlavaan',
        'Viherlinna': 'Viherlinnaan',
        'Toikola': 'Toikolaan',
        'Koivulahti': 'Koivulahteen'
      },
      tracks: '',
      xml_st: `<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" xmlns:emo="http://www.w3.org/2009/10/emotionml" version="1.0" xml:lang="en-US">`,
      xml_en: `</speak>`,
      xml_train_st: `<voice name="fi-FI-HarriNeural">`,
      xml_train_en: `</voice>`,
      xml_plat_st: `<voice name="fi-FI-SelmaNeural">`,
      xml_plat_en: `</voice>`,
      presets: [
        { stations: [], tracks: "" }
      ]
    }
  },
  methods: {
    add(name) {
      this.followingStations.push(name)
    },
    remove(name) {
      this.followingStations.splice(this.followingStations.findLastIndex(x => x === name), 1)
    },
    generateCallingAt(offset) {
      const st = [...this.followingStations]
      st.shift()
      st.pop()
      if (offset) st.splice(0, offset || 0)
      let and = ""
      if (st.length < 1) return ''
      if (st.length > 1) {
        and = `, ja ${st.pop()}`
      }
      return this.callingAtAll ? 'Pysähdymme kaikilla asemalla. ' : ['Pysäkkimme ovat: ', st.map(x => x).join(', '), and, '.'].join('')
    }
  },
  computed: {
    tracksArr() {
      return this.tracks.split(',').map(x => parseInt(x.trim())) || []
    },
    announcedStations() {
      return this.announcedStationsArr.join('')
    },
    announcedStationsArr() {
      console.log(this.followingStations);
      const st = [...this.followingStations]
      st.shift()
      const last = st.pop()
      return [st.map(x => [['Seuraava: ', x, '. '].join(''), x, '. '].join('')).join('. '), last && ['Seuraava: ', last, '. '].join('')]
    },
    stationAnnouncements() {
      const st = [...this.followingStations]
      st.pop()
      const prefix = `${this.trainName} ${this.direction} lähtee radalta`
      const terminus = `${this.trainName} ${this.direction} saapuu radalta ${this.tracksArr[this.followingStations.length - 1] || 1}. Juna päättyy tähän.`
      const stAnns = st.map((x, i) => {

        return `${prefix} ${this.tracksArr[i] || 1}. ${this.generateCallingAt(i)}`
      })
      return [...stAnns, terminus]
    },
    callingAt() {
      return this.generateCallingAt()
    },
    direction() {
      const st = [...this.followingStations]
      return this.directionDict[st.pop()] || ''
    }
  }
};
</script>

<style>
.row,
.column {
  display: flex;
}

.row {
  flex-direction: row;
  align-items: flex-end;
}

.column {
  flex-direction: column;
}
</style>