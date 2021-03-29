<template>

<div class="box">
  <div class="fill" @click="$emit('bgclick')">
  </div>
  <div class="content">
    <div class="container-fluid p-3">
      <div class="row">
        <div class="col" @click="$emit('bgclick')">
        </div>
        <div class="col-12 col-sm-9 col-md-6 col-lg-auto bg-light">
          <div style="">
            <slot></slot>
          </div>
        </div>
        <div class="col" @click="$emit('bgclick')">
        </div>
      </div>
    </div>
  </div>
  <div class="fill" @click="$emit('bgclick')">
  </div>
</div>
</template>

<script>
export default {
  name: "",
  props: {
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.box {
  display: flex;
  flex-flow: column;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.3);
  z-index: 10000000;
}

.box .content {
  flex: 0 1 auto;
  /* The above is shorthand for:
  flex-gmodal-row: 0,
  flex-shrink: 1,
  flex-basis: auto
  */
}

.box .fill {
  flex: 1 1 auto;
}
</style>
