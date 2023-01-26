<script setup>
import StationButton from "./components/StationButton.vue";
</script>

<template>
  <div id="app">

    <div>
      <select v-model="slotsSelect">
        <option value="">NONE</option>
        <option v-for="slot in storageSlots" :key="slot" :value="slot">{{ slot }}</option>
        <option value="add">ADD SLOT</option>
        <option value="remove">REMOVE SLOT</option>
      </select>
      <button @click="storageSave">save</button>
      <button @click="storageLoad">load</button>
    </div>
    <hr />

    <div class="row">
      <div class="column">
        <StationButton name="Myllylä" @add="add" @remove="remove" />
        <StationButton name="Aikava" @add="add" @remove="remove" />
        <StationButton name="Vähämaa" @add="add" @remove="remove" />
      </div>
      <div class="column">
        <StationButton name="Honkala" @add="add" @remove="remove" />
        <StationButton name="Haapasalmi" @add="add" @remove="remove" />
        <StationButton name="Santala" @add="add" @remove="remove" />
        <StationButton name="Tervapuro" @add="add" @remove="remove" />
        <StationButton name="Jylppy" @add="add" @remove="remove" />
      </div>
      <StationButton name="Lassila" @add="add" @remove="remove" />
      <StationButton name="Järvela" @add="add" @remove="remove" />
      <div class="column">
        <StationButton name="Koivulahti" @add="add" @remove="remove" />
        <StationButton name="Vaaranmaa" @add="add" @remove="remove" />
        <StationButton name="Korkia-aho" @add="add" @remove="remove" />
        <StationButton name="Mattila" @add="add" @remove="remove" />
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
        <StationButton name="Koivulahti " @add="add" @remove="remove" />
        <StationButton name="Viljakanpohja" @add="add" @remove="remove" />
        <StationButton name="Toikola" @add="add" @remove="remove" />
      </div>
    </div>

    <hr />

    {{ followingStations.toString() }}

    <input type="text" name="train" id="train" v-model="trainName">
    <input type="checkbox" name="callingAtAll" id="callingAtAll" v-model="callingAtAll">
    <label for="callingAtAll">Pysähdymme kaikilla asemalla</label>

    <table>
      <thead>
        <tr>
          <th>station</th>
          <th>track</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(station, i) in followingStations" :key="i">
          <td>{{ station }}</td>
          <td><input type="number" min="1" v-model.number="tracksArr[i]" /></td>
        </tr>
      </tbody>
    </table>

    <div v-if="false">
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

    </div>

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
      tracksArr: [],
      xml_st: `<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" xmlns:emo="http://www.w3.org/2009/10/emotionml" version="1.0" xml:lang="en-US">`,
      xml_en: `</speak>`,
      xml_train_st: `<voice name="fi-FI-HarriNeural">`,
      xml_train_en: `</voice>`,
      xml_plat_st: `<voice name="fi-FI-SelmaNeural">`,
      xml_plat_en: `</voice>`,
      slotsSelect: '',
      storageSlots: Object.keys(localStorage).sort(),
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
      const index = this.followingStations.findLastIndex(x => x === name)
      this.tracksArr.splice(index, 1)
      this.followingStations.splice(index, 1)
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
    },
    storageSave() {
      if (!confirm(`Are you sure you want to override ${this.slotsSelect}?`)) {
        return
      }
      if (!this.slotsSelect || this.slotsSelect === 'add' || this.slotsSelect === 'remove') {
        alert('Please select a slot! Settings have not been saved.')
        return
      }
      localStorage.setItem(String(this.slotsSelect), JSON.stringify({
        followingStations: this.followingStations,
        trainName: this.trainName,
        callingAtAll: this.callingAtAll,
        tracksArr: this.tracksArr
      }))
      alert(`Saved settings to ${this.slotsSelect}!`)
    },
    storageLoad() {
      if (!this.slotsSelect || this.slotsSelect === 'add' || this.slotsSelect === 'remove') {
        alert('Please select a slot! Settings have not been loaded.')
        return
      }
      try {
        const settings = JSON.parse(localStorage.getItem(this.slotsSelect))
        if (settings.followingStations) this.followingStations = settings.followingStations
        if (settings.trainName) this.trainName = settings.trainName
        if (settings.callingAtAll) this.callingAtAll = settings.callingAtAll
        if (settings.tracksArr) this.tracksArr = settings.tracksArr
        alert(`Loaded settings from ${this.slotsSelect}!`)
      } catch (error) {
        console.log(error);
        alert('Loading failed, slot is corrupted!')
      }
    }
  },
  watch: {
    slotsSelect() {
      switch (this.slotsSelect) {
        case 'add':
          const newSlotName = prompt('Add a new slot:')
          if (newSlotName && newSlotName !== 'add' && newSlotName !== 'remove' && !localStorage[String(newSlotName)]) {
            localStorage.setItem(String(newSlotName), '')
            this.storageSlots.push(String(newSlotName))
            this.slotsSelect = String(newSlotName)
          } else {
            alert("Invalid name! Slot has not been created.")
            this.slotsSelect = ''
          }
          break;
        case 'remove':
          const slotName = String(prompt('Remove an existing slot:'))
          localStorage.removeItem(slotName)
          this.storageSlots = this.storageSlots.filter(x => x !== slotName)
          this.slotsSelect = ''
          break;
        default:
          break;
      }
    }
  },
  computed: {
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
      const terminus = `${this.trainName} ${this.direction} saapuu radalta ${this.tracksArr[this.followingStations.length - 1] || 1}.`
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