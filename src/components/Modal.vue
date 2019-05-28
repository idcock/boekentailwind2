<template>
  <div>
    <div
      class="overlay flex fixed inset-0 w-screen h-screen z-50 justify-center items-center"
      @click.self="$emit('sluiten')"
    >
      <div class="w-2/5 bg-blue-100 p-8">
        <slot> </slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {};
</script>

<style scoped>
.overlay {
  background-color: rgba(0, 0, 0, 0.2);
}
</style>
