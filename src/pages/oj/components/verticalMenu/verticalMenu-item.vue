<template>
  <li @click.stop="handleClick" :class="{disabled: disabled, result: isResult}">
    <slot></slot>
  </li>
</template>

<script>
  import Emitter from '../mixins/emitter'

  export default {
    name: 'VerticalMenu-item',
    mixins: [Emitter],
    props: {
      route: {
        type: [String, Object]
      },
      disabled: {
        type: Boolean,
        default: false
      },
      isResult: {
        type: Boolean,
        default: false
      }
    },
    methods: {
      handleClick () {
        if (this.isResult) {
          this.$confirm('Are you sure you want to end the assessment?', 'Confirm', {
            confirmButtonText: 'Confirm',
            cancelButtonText: 'Cancel',
            type: 'warning'
          }).then(() => {
            window.open(`${process.env.PROCTOR_URL}/contest/result`, '_self')
          })
        } else if (this.route) {
          this.dispatch('VerticalMenu', 'on-click', this.route)
        }
      }
    }
  }
</script>

<style scoped lang="less">
  .disabled {
    /*background-color: #ccc;*/
    opacity: 1;
    /*cursor: not-allowed;*/
    pointer-events: none;
    color: #ccc;
    &:hover {
      border-left: none;
      color: #ccc;
      background: #fff;
    }
  }

  .result{
    background-color: #19be6b;
    color: white;
    &:hover {
      background: #f5f5f5;
      color: #ed3f14;
      border-left: 2px solid #ed3f14;
    }
  }

  li {
    border-bottom: 1px dashed #e9eaec;
    color: #495060;
    display: block;
    text-align: left;
    padding: 15px 20px;
    &:hover {
      background: #f8f8f9;
      border-left: 2px solid #5cadff;
      color: #2d8cf0;
    }
    & > .ivu-icon {
      font-size: 16px;
      margin-right: 8px;
    }
    &:last-child {
      border-bottom: none;
    }
  }
</style>
