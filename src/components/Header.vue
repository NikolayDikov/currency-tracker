<template>
    <div class="nav hsp">
        <h1>{{ msg }}</h1>
    </div>
</template>

<script>
export default {
  name: 'Header',
  props: {
    msg: String
  }
}
</script>

<style scoped>
.hsp {
    position: absolute;
    z-index: 501;
    top: 0px;
    width: 100%;
    background: none;
    transition: background 0.2s linear 0s;
}
</style>