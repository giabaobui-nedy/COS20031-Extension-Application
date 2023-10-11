<template>
  <div class="range-component">
    <div class="heading-container">
      <h3>Range {{ range.rangeNum }}</h3>
      <p>Distance: {{ range.distance }} - Face Type: {{ range.faceType }}</p>
      <button @click="resetRange">Reset Range</button>
      <button @click="submitRange">Submit Range</button>
    </div>
    <div v-if="!isSubmitted">
      <div class="end-components-container">
        <endComponent ref="endComponents" v-for="endNumber in parseInt(range.numberOfEnds)" :key="endNumber"
          :endNumber="endNumber" @scores-submitted="handleScoresSubmitted" @scores-reset="handleScoresReset"
          v-show="endNumber === selectedEnd">
        </endComponent>
      </div>
      <div class="end-navigation">
        <span>{{ selectedEnd }} / {{ range.numberOfEnds }}</span>
        <button @click="incrementEnd" :disabled="selectedEnd >= range.numberOfEnds">Next</button>
      </div>
      <div>
        {{ msg }}
      </div>
    </div>
    <div v-else>
      <div>
        <table>
          <caption>Result for Range {{ rangeRecord.rangeNumber }}</caption>
          <thead>
            <tr>
              <th>End</th>
              <th>Arrow 1</th>
              <th>Arrow 2</th>
              <th>Arrow 3</th>
              <th>Arrow 4</th>
              <th>Arrow 5</th>
              <th>Arrow 6</th>
            </tr>
          </thead>
          <tbody>
            <template v-for="(endRecord, index) in rangeRecord.endRecords" :key="index">
              <tr>
                <td>{{ endRecord.endNumber }}</td>
                <td v-for="(score, scoreIndex) in endRecord.scores" :key="scoreIndex">
                  {{ score }}
                </td>
              </tr>
            </template>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import endComponent from './endComponent.vue'

export default {
  name: 'rangeComponent',
  components: {
    endComponent
  },
  props: {
    range: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      selectedEnd: 1,
      msg: '',
      isSubmitted: false
    }
  },
  methods: {
    incrementEnd() {
      if (this.rangeRecord.endRecords[this.selectedEnd - 1] !== undefined) {
        this.selectedEnd++;
      } else {
        this.msg = "You must submit the current End Record before proceeding to the next one."
      }
    },
    handleScoresSubmitted(endRecord) {
      this.rangeRecord.endRecords[parseInt(endRecord.endNumber) - 1] = endRecord;
      this.msg = '';
      // Handle the submitted scores data here
      console.log(this.rangeRecord);
    },
    handleScoresReset(endNumber) {
      this.rangeRecord.endRecords[parseInt(endNumber) - 1] = undefined;
      this.msg = '';
      // Handle the reset scores data here
      console.log(this.rangeRecord);
    },
    resetRange() {
      this.selectedEnd = 1;
      this.msg = '';
      this.isSubmitted = false;
      this.rangeRecord.endRecords = Array(parseInt(this.range.numberOfEnds));
      // Reset the scores in each endComponent
      this.$refs.endComponents.forEach((endComponent) => {
        if (endComponent.isSubmitted) {
          endComponent.resetScore();
        }
      });
    },
    submitRange() {
      if (this.rangeRecord.endRecords.some(endRecord => endRecord !== null)) {
        this.isSubmitted = true;
        this.rangeRecord.endRecords = this.rangeRecord.endRecords.filter(endRecord => {
          return endRecord !== null;
        });
      } else {
        this.msg = "You need at least one End record to submit this Range record";
      }
    },
  },
  computed: {
    rangeRecord() {
      return {
        rangeNumber: this.range.rangeNum,
        endRecords: Array(parseInt(this.range.numberOfEnds))
      }
    }
  }
};
</script>
