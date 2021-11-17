<template>
  <div class="container">
    <input 
      :id="label"
      :value="num"
      :class="inputClass"
    />
    <label :for="label">
      <slot></slot>
    </label>
  </div>
</template>

<script>
export default {
  props: {
    num: Number,
    label: String,
    started: Boolean
  },
  emits: ['update:num'],
  computed: {
    inputClass() {
      return { 'wide-input': this.num > 99}
    },
    value: {
      num: {
        get() {
          return this.num
        },
        set(num) {
          this.$emit('update:num', num)
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  align-self: flex-end;
}

input {
  font-size: 8rem;
  width: 10rem;
  border: none;
  background-color: inherit;
  border-bottom: .25rem solid black;

  &:focus-visible {
    outline: none;
  }
}

@media (min-width: 20em) and (max-width: 30em) {
  label {
    padding-bottom: 2rem;
  }

  input {
    text-align: center;
    font-size: 6rem;
    width: 25%;
    border-bottom: .125rem solid black;
  }
}
</style>