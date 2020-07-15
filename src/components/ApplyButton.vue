<template>
  <span>
    <button
      class="btn"
      type="button"
      v-bind:class="{disabled: lockApply}"
      v-on:click="notifyToApply"
    >
      <span>修改內容</span>
    </button>
    <button
      class="btn"
      v-bind:class="{disabled: lockCancel}"
      type="button"
      v-on:click="notifyToCancel"
    >
      <span>取消</span>
    </button>
  </span>
</template>

<script>
export default {
  data() {
    return {};
  },
  props: {
    lockCancel: {
      type: Boolean,
      default: false
    },
    lockApply: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    notifyToApply() {
      this.$emit("apply");
    },
    notifyToCancel() {
      this.$emit("cancel");
    }
  }
};
</script>

