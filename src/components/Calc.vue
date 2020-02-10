<template>
  <section class="calc">
    <form>
      <div class="inputs-wrapper">
        <input
          v-model="hours"
          class="time-input"
          type="number"
          min="0"
          max="100"
          name="hours"
          @:input="changeValue"
        />
        <label for="hours">h</label>
        <input
          v-model="mins"
          class="time-input"
          type="number"
          min="0"
          max="59"
          name="mins"
          @:input="changeValue"
        />
        <label for="mins">m</label>
        <input
          v-model="secs"
          class="time-input"
          type="number"
          min="0"
          max="59"
          name="secs"
          @:input="changeValue"
        />
        <label for="secs">s</label>
      </div>
      <fieldset class="ratio-input">
        <label for="selectedRatio">Playback speed:</label>
        <select class="selected-ratio" v-model="selectedRatio">
          <option v-for="ratio in ratios" v-bind:key="ratio" v-bind:value="ratio">{{
            ratio
          }}</option>
        </select>
      </fieldset>
    </form>
    <div class="results-wrapper">
      <p class="result">
        <span class="desc">Converted Duration:</span>
        <span class="formatted-result">{{ formatDuration(convertedDuration) }}</span>
      </p>
      <p class="result">
        <span class="desc">Savings:</span>
        <span class="formatted-result">{{ formatDuration(savings) }}</span>
      </p>
    </div>
  </section>
</template>

<script>
import { formatDistanceStrict } from 'date-fns'
import { addSeconds, addMinutes, addHours } from 'date-fns/fp'
import { flow } from 'lodash/fp'

const changeValue = e => {
  // TODO - figure out why you can return
  // whatever you want here
  /* return parseInt(e.target.data, 10) */
  e = false
  return e
}

// format seconds to `hh:mm:ss`
// c/o https://github.com/date-fns/date-fns/issues/284#issuecomment-555838603
// with modifications
// TODO - refactor this without locales
const formatDuration = (sec, locale = 'en-us') => {
  if (sec) {
    const [delimiter = ':'] = new Date().toLocaleTimeString(locale).match(/\b[:.]\b/) || []
    const cachedIntl = Intl.NumberFormat(locale, { minimumIntegerDigits: 1 })
    const cachedIntlPadded = Intl.NumberFormat(locale, {
      minimumIntegerDigits: 2
    })

    const isNegativeSavings = sec < 0
    const duration = Math.round(Math.abs(sec))
    const hours = Math.floor(duration / 3600)
    const minutes = Math.floor(duration / 60) % 60
    const seconds = duration % 60
    const indexToPad = hours ? 0 : 1
    const timeFormat = [hours, minutes, seconds]
      .map((val, i) => {
        // only pad if seconds or (if has hours) minutes
        return val < 10 && i > indexToPad ? cachedIntlPadded.format(val) : cachedIntl.format(val)
      })
      .filter((val, i) => {
        // whatever is first (hours, minutes), we check to see if 0
        if (i === 0) {
          return !(val === '00' || val === '0')
        }
        // otherwise we dont mind filling with 00
        return true
      })
      .join(`${delimiter}`)
    return !isNegativeSavings ? timeFormat : '- ' + timeFormat
  } else {
    return '00:00:00'
  }
}

const getDifferenceInSeconds = (end, start = initialState.start) => {
  // formatDistanceStrict() gets (end - start) in seconds
  // but returns a string as "{{ n }} seconds"
  // split on " " and take the first element
  return formatDistanceStrict(end, start, {
    unit: 'second'
  }).split(' ')[0]
}

const computeDuration = vm => {
  const { hours, mins, secs } = vm
  const addValues = flow(addSeconds(secs), addMinutes(mins), addHours(hours))
  const end = addValues(initialState.start)

  return getDifferenceInSeconds(end)
}

const convertDuration = vm => {
  const { duration, selectedRatio } = vm
  const converted = duration / selectedRatio
  return converted
}

const makeRatios = () => {
  return Array.from({ length: 20 }, (_, i) => {
    return parseFloat((i + 1) * 0.1).toFixed(1)
  })
}

const initialState = {
  selectedRatio: 1.2,
  ratios: makeRatios(), // generated ratios
  start: new Date(0),
  hours: 0,
  mins: 10,
  secs: 0
}

export default {
  name: 'Calc',
  data: () => {
    return initialState
  },
  computed: {
    duration: computeDuration,
    convertedDuration: convertDuration,
    savings: vm => {
      const { duration, convertedDuration } = vm
      return duration - convertedDuration
    }
  },
  methods: {
    changeValue,
    formatDuration
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calc {
  max-width: 500px;
  margin: 0 auto;
}
label {
  font-size: 3rem;
}

fieldset {
  border: none;
  margin: 0;
  padding: 0;
}

.inputs-wrapper {
  display: flex;
  align-items: baseline;
}

.time-input {
  background: #272e38;
  color: #cacaca;
  border: none;
  font-size: 6rem;
  text-align: right;
  width: 100%;
}

.ratio-input {
  background: #272e38;
  color: #cacaca;
  display: block;
}

.selected-ratio {
  font-size: 2rem;
  margin: 0 1em;
  background: #272e38;
  color: #cacaca;
}
.results-wrapper {
  display: flex;
  flex-direction: column;
}
.result {
  display: flex;
  font-size: 2rem;
  justify-content: space-between;
}
.desc {
  display: block;
}
</style>
