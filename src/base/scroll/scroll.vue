<template>
  <div ref="wrapper">
    <slot></slot>
  </div>

</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'

  export default {
    // 传一些基础数据
    props: {
      probeType: {
        type: Number,
        default: 1
      },
      click: {
        type: Boolean,
        default: true
      },
      data: {
        type: Array,
        default: null
      },
      listenScroll: {
        type: Boolean,
        default: false
      },
      pullup: {
        type: Boolean,
        default: false
      },
      beforeScroll: {
        type: Boolean,
        default: false
      },
      refreshDelay: {
        type: Number,
        default: 20
      }
    },
    // 确保DOM渲染后再初始化scroll组件
    mounted () {
      setTimeout(() => {
        this._initScroll()
      }, 20)
    },
    methods: {
      _initScroll () {
        // 如果wrapper是undefined的话
        if (!this.$refs.wrapper) {
          return
        }
        this.scroll = new BScroll(this.$refs.wrapper, {
          probeType: this.probeType,
          click: this.click
        })
        if (this.listenScroll) {
          // 如果为true的话，用$emit向外派发滚动事件
          let me = this
          this.scroll.on('scroll', (pos) => {
            me.$emit('scroll', pos)
          })
        }
        // 如果pullup设为true的话
        if (this.pullup) {
          // scroll有一个scrollEnd事件
          this.scroll.on('scrollEnd', () => {
            // 如果滑到底部的这个距离，就派发事件scrollToEnd
            if (this.scroll.y <= (this.scroll.maxScrollY + 50)) {
              this.$emit('scrollToEnd')
            }
          })
        }
        // 当我开始滑动时，键盘应当消失，所以我们要监听滚动事件
        if (this.beforeScroll) {
          this.scroll.on('beforeScrollStart', () => {
            this.$emit('beforeScroll')
          })
        }
      },
      // 他们都是调用封装好的better-scroll里的方法
      enable () {
        this.scroll && this.scroll.enable()
      },
      disenable () {
        this.scroll && this.scroll.disenable()
      },
      refresh () {
        this.scroll && this.scroll.refresh()
      },
      scrollTo () {
        this.scroll && this.scroll.scrollTo.apply(this.scroll, arguments)
      },
      scrollToElement () {
        this.scroll && this.scroll.scrollToElement.apply(this.scroll, arguments)
      }
    },
    // 监视data变化，有变化重新刷新
    watch: {
      data () {
        setTimeout(() => {
          this.refresh()
        }, this.refreshDelay)
      }
    }
  }
</script>
