<template>
  <div id="app">
    <vue-snotify/>
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App',
  mounted() {
    this.$mqtt.subscribe('#')
  }
}
</script>

<style lang="scss">
.customNoti {
  // Style of the notification itself
  padding: 10px;
  margin: 5px;

  font-size: 12px;
  border-radius: 1px;

  color: #ffffff;
  background: #44a4fc;
  border-left: 5px solid #187fe7;

  &.warn {
    background: #ffb648;
    border-left-color: #f48a06;
  }

  &.error {
    background: #e54d42;
    border-left-color: #b82e24;
  }

  &.success {
    background: #68cd86;
    border-left-color: #42a85f;
  }
  .notification-title {
    // Style for title line
    letter-spacing: 1px;
    text-transform: uppercase;
    font-size: 16px;
  }

  .notification-content {
    // Style for content
    margin-top: 5px;
    font-size: 15px;
  }
}
</style>
