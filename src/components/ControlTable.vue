<template>
  <div>
    <table class="table table-hover">
      <thead>
        <tr>
          <th width="30px" v-if="checkbox">
            <!-- <input
              v-if="checkboxAll"
              type="checkbox"
              v-bind:checked="controlSelectAll"
              v-on:change="controlSupervisor()"
            >-->
          </th>
          <slot name="head"></slot>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(n, index) in nums" v-bind:key="n" v-bind:class="{active: isChecked[index]}">
          <td v-if="checkbox">
            <input type="checkbox" v-model="isChecked[index]" v-on:click="checked($event, index)">
          </td>
          <slot name="body" :index="index" :isChecked="isChecked[index]"></slot>
        </tr>
      </tbody>
      <tfoot></tfoot>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isChecked: {}
    };
  },
  props: {
    nums: Number,
    dbChanges: {
      default: Array,
      type: Array
    },
    checkbox: {
      default: true,
      type: Boolean
    },
    checkboxAll: {
      default: true,
      type: Boolean
    }
  },
  computed: {
    controlSelectAll() {
      let select = _.countBy(_.values(this.isChecked), Boolean).true;
      if (!select) {
        select = 0;
      }
      return select === this.nums && this.nums > 0;
    }
  },
  methods: {
    checked(event, index) {
      for (let i = 0; i < this.nums; i++) {
        if (index !== i) {
          this.$set(this.isChecked, i, false);
        }
      }
      this.$emit("isChecked", index, event.target.checked);
    },
    controlSupervisor() {
      let isChecked = !this.controlSelectAll;
      for (let num = 0; num < this.nums; num++) {
        this.isChecked[num] = isChecked;
        this.$emit("isChecked", num, isChecked);
      }
    },
    initChecks() {
      this.isChecked = {};
      for (let i = 0; i < this.nums; i++) {
        this.$set(this.isChecked, i, false);
      }
    }
  },
  created() {
    this.initChecks();
  },
  watch: {
    nums() {
      this.initChecks();
    }
  }
};
</script>

<style>
th.align-center,
td.align-center {
  text-align: center;
}

.table thead {
  font-size: 14px;
  font-weight: bold;
  background-color: #6e5b52;
  color: white;
}

.table > thead > tr > th,
.table > tbody > tr > th,
.table > tfoot > tr > th,
.table > thead > tr > td,
.table > tbody > tr > td,
.table > tfoot > tr > td {
  padding: 0.8vw;
}

.table-hover > tbody > tr:hover {
  background-color: #e8ce53;
}

.table-hover > tbody > tr.active > td,
.table-hover > tbody > tr.active:hover > th {
  background-color: #f5eeeb;
}
</style>
