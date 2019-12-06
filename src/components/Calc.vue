<template>
  <div class="calc">
    <input
      class="time-input"
      type="number"
      min="0"
      max="100"
      name="hours"
      v-model="hours"
      v-on:input="changeHours"
    />
    <label for="hours">h</label>
    <input
      class="time-input"
      type="number"
      min="0"
      max="59"
      name="mins"
      v-model="mins"
      v-on:input="changeMins"
    />
    <label for="mins">m</label>
    <input
      class="time-input"
      type="number"
      min="0"
      max="59"
      name="secs"
      v-model="secs"
      v-on:input="changeSecs"
    />
    <label for="secs">s</label>
    <div class="ratio-input">
      <label for="selectedRatio">Playback speed:</label>
      <select class="selected-ratio" v-model="selectedRatio">
        <option v-for="ratio in ratios" v-bind:key="ratio" v-bind:value="ratio">{{ ratio }}</option>
      </select>
    </div>
    <!-- <div class="duration">Total Duration: {{ duration }}</div> -->
    <div class="duration">Converted Duration: {{ formatDuration(convertedDuration) }}</div>
    <div class="savings">Savings: {{ formatDuration(savings) }}</div>
  </div>
</template>

<script>
import { formatDistanceStrict } from "date-fns";
import { addSeconds, addMinutes, addHours } from "date-fns/fp";
import { flow } from "lodash/fp";

// const DATE_FORMAT = "hh:mm:ss";

const changeHours = e => {
  const h = parseInt(e.target.data, 10);
  return h;
};

const changeMins = e => {
  const m = parseInt(e.target.data, 10);
  return m;
};

const changeSecs = e => {
  const s = parseInt(e.target.data, 10);
  return s;
};

// format seconds to `hh:mm:ss`
// c/o https://github.com/date-fns/date-fns/issues/284#issuecomment-555838603
// with modifications
const formatDuration = (sec, locale = "en-us") => {
  if (sec) {
    const [delimiter = ":"] =
      new Date().toLocaleTimeString(locale).match(/\b[:.]\b/) || [];
    const cachedIntl = Intl.NumberFormat(locale, { minimumIntegerDigits: 1 });
    const cachedIntlPadded = Intl.NumberFormat(locale, {
      minimumIntegerDigits: 2
    });

    const duration = Math.round(sec);
    const hours = Math.floor(duration / 3600);
    const minutes = Math.floor(duration / 60) % 60;
    const seconds = duration % 60;
    const indexToPad = hours ? 0 : 1;
    const timeFormat = [hours, minutes, seconds]
      .map((val, i) => {
        // only pad if seconds or (if has hours) minutes
        return val < 10 && i > indexToPad
          ? cachedIntlPadded.format(val)
          : cachedIntl.format(val);
      })
      .filter((val, i) => {
        // whatever is first (hours, minutes), we check to see if 0
        if (i === 0) {
          return !(val === "00" || val === "0");
        }

        // otherwise we dont mind filling with 00
        return true;
      })
      .join(`${delimiter}`);

    return timeFormat;
  }
};

const getDifferenceInSeconds = (end, start = initialState.start) => {
  // formatDistanceStrict() gets (end - start) in seconds
  // but returns a string as "{{ n }} seconds", so we need to split
  return formatDistanceStrict(end, start, {
    unit: "second"
  }).split(" ")[0];
};

const computeDuration = vm => {
  const { hours, mins, secs } = vm;
  const addValues = flow(
    addSeconds(secs),
    addMinutes(mins),
    addHours(hours)
  );
  const end = addValues(initialState.start);

  return getDifferenceInSeconds(end);
};

const convertDuration = vm => {
  const { duration, selectedRatio } = vm;
  const converted = duration / selectedRatio;
  return converted;
};

const makeRatios = () => {
  return Array.from({ length: 20 }, (x, i) => {
    return parseFloat((i + 1) * 0.1).toFixed(1);
  });
};

const initialState = {
  selectedRatio: 1.2,
  ratios: makeRatios(), // generated ratios
  start: new Date(0),
  hours: 0,
  mins: 0,
  secs: 0
};

export default {
  name: "Calc",
  data: () => {
    return initialState;
  },
  computed: {
    duration: computeDuration,
    convertedDuration: convertDuration,
    savings: vm => {
      const { duration, convertedDuration } = vm;
      return duration - convertedDuration;
    }
  },
  methods: {
    changeHours: changeHours,
    changeMins: changeMins,
    changeSecs: changeSecs,
    formatDuration: formatDuration
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* .calc {
  display: block;
} */
label {
  font-size: 10vh;
}

.ratio-input {
  display: block;
}

.selected-ratio {
  font-size: 5vh;
  margin: 0 1em;
}

.time-input {
  border: none;
  font-size: 14vh;
  text-align: right;
}

.duration {
  display: block;
}

.savings {
  display: block;
}
</style>