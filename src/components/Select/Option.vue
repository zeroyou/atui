<template>
  <div v-show="show"
       :class="optionClassObj"
       @mouseup.prevent.stop="handleClick">
    <slot></slot>
  </div>
</template>

<script>
  // import Icon from '../Icon/
  // TODO: 子组件不应该知道父组件的内容，所有业务逻辑应该拿到父组件中去
  export default {
    name: 'Option',
    props: {
      value: [String, Number],
      disabled: Boolean,
      prefixCls: {
        type: String,
        default: 'atui'
      }
    },
    data () {
      return {
        active: false
      }
    },
    computed: {
      chosen () {
        let me = this
        return me.$parent.$parent.selectedOptions.some((item) => {
          return item.value === this.value
        })
      },
      show () {
        let searchText = this.$parent.$parent.searchText.trim()
        if (searchText.length && this.$parent.$parent.multiple) {
          return this.$el.innerText.indexOf(searchText) >= 0
        }
        return true
      },
      optionClassObj () {
        let { prefixCls, active, disabled, chosen } = this
        let classObj = {}

        classObj[prefixCls + '-dropdown-option'] = true
        classObj[prefixCls + '-dropdown-option-disabled'] = disabled
        classObj[prefixCls + '-dropdown-option-active'] = active
        classObj[prefixCls + '-dropdown-option-chosen'] = chosen

        return classObj
      }
    },
    mounted () {
      let option = {
        label: this.$el.innerText.trim(),
        value: this.value,
        disabled: this.disabled
      }
      this.$parent.$parent.$data.options.push(option)
      let value = this.$parent.$parent.value
      if ((Array.isArray(value) && value.indexOf(this.value) >= 0) || value === this.value) {
        this.$parent.$parent.selectedOptions.push(option)
      }
    },
    methods: {
      handleClick () {
        if (this.disabled) {
          return
        }
        let option = {
          label: this.$el.innerText,
          value: this.value
        }
        this.$parent.$parent.$emit('option-change', option)
      }
    },
    events: {
      valueChange (val) {
        if (val === this.value && !this.disabled) {
          const option = {
            label: this.$el.innerText,
            value: this.value,
            disabled: this.disabled
          }
          this.$parent.$parent.selectedOptions = [option]
          this.chosen = true
        }
      }
    }
  }
</script>

