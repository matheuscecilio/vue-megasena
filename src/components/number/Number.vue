<template>
  <div 
    class="number"
    :class="!!picked ? 'picked' : 'not-picked'">
    {{ value }}
</div>
</template>

<script>
export default {
    name: 'number',
    props: {
        picked: {
            type: Boolean,
            default: false
        },
        value: Number
    }
}
</script>

<style>
.number {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  border: 3px solid black;
  font-family: monospace;
  font-size: 30px;
  font-weight: bold;
  margin-right: 10px;
}

.picked {
  background: lightgreen;
}

.not-picked {
  background: lightgoldenrodyellow;
}
</style>