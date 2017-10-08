<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <table>
      <tr>
        <th>Task</th>
        <th>Optimistic</th>
        <th>Usual</th>
        <th>Pessimistic</th>
        <th>Estimate</th>
        <th>1SD</th>
      </tr>
      <tr v-for="estimate in estimations">
        <td>{{ estimate.taskName }}</td>
        <td>{{ estimate.optimistic }}</td>
        <td>{{ estimate.usual }}</td>
        <td>{{ estimate.pessimistic }}</td>
        <td>{{ estimate.estimate }}</td>
        <td>{{ estimate.variance }}</td>
      </tr>
      <tr>
        <td colspan="4"></td>
        <td>{{ totalEstimate() }}</td>
        <td>{{ totalSD() }}</td>
      </tr>
      <tr>
        <td><input v-model="taskName" /></td>
        <td><input type="number" v-model="optimistic" /></td>
        <td><input type="number" v-model="usual" /></td>
        <td><input type="number" v-model="pessimistic" /></td>
        <td><button v-on:click="addTask">Save</button></td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data() {
    return {
      msg: 'PERT Estimator',
      optimistic: 0,
      usual: 0,
      pessimistic: 0,
      taskName: '',
      estimations: [],
    };
  },
  methods: {
    addTask() {
      this.estimations.push({
        taskName: this.taskName,
        optimistic: this.optimistic,
        usual: this.usual,
        pessimistic: this.pessimistic,
        estimate: this.calculateEstimate(),
        variance: this.calculateVariance(),
      });
    },
    calculateEstimate() {
      return ((Number(this.optimistic) + Number(this.pessimistic) +
            (4 * Number(this.usual))) / 6).toFixed(2);
    },
    calculateVariance() {
      return ((this.pessimistic - this.optimistic) ** 2) / (6 ** 2);
    },
    isNumber(event) {
      const evt = event || window.event;
      const charCode = (evt.which) ? evt.which : evt.keyCode;
      if ((charCode > 31 && (charCode < 48 || charCode > 57)) && charCode !== 46) {
        evt.preventDefault();
      } else {
        return true;
      }
      return false;
    },
    totalEstimate() {
      if (this.estimations.length < 1) {
        return 0;
      }
      return this.estimations.reduce((total, e) => total + Number(e.estimate), 0);
    },
    totalSD() {
      if (this.estimations.length < 1) {
        return 0;
      }
      return this.estimations.reduce((total, e) => total + Number(e.variance), 0);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
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
