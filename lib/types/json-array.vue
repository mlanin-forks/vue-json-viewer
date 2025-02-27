<script>
import JsonBox from '../json-box'

export default {
  name: 'JsonArray',
  props: {
    jsonValue: {
      type: Array,
      required: true
    },
    keyName: {
      type: String,
      default: ''
    },
    depth: {
      type: Number,
      default: 0
    },
    sort: Boolean,
    expand: Boolean,
    previewMode: Boolean,
  },
  data() {
    return {
      value: []
    }
  },
  watch: {
    jsonValue(newVal) {
      this.setValue(newVal);
    }
  },
  mounted() {
    this.setValue(this.jsonValue);
  },
  methods: {
    setValue(vals, index = 0) {
      if (index === 0) {
        this.value = [];
      }
      setTimeout(() => {
        if (vals.length > index) {
          this.value.push(vals[index]);
          this.setValue(vals, index + 1);
        }
      }, 0);
    },
    toggle() {
      this.$emit('update:expand', !this.expand)

      try {
        this.$el.dispatchEvent(new Event('resized'))
      } catch (e) {
        // handle IE not supporting Event constructor
        var evt = document.createEvent('Event')
        evt.initEvent('resized', true, false)
        this.$el.dispatchEvent(evt)
      }
    },
    
  },
  render (h) {
    let elements = []

    if (!this.previewMode && !this.keyName) {
      elements.push(h('span', {
        class: {
          'jv-toggle': true,
          'open': !!this.expand,
        },
        on: {
          click: this.toggle
        }
      }))
    }

    elements.push(h('span', {
      class: {
        'jv-item': true,
        'jv-array': true,
      },
      domProps: {
        innerText: '['
      }
    }))
    if (this.expand) {
      this.value.forEach((value, key) => {
        elements.push(h(JsonBox, {
          key,
          style: {
            display: this.expand ? undefined : 'none'
          },
          props: {
            sort: this.sort,
            keyName: `${key}`,
            depth: this.depth + 1,
            value,
            previewMode: this.previewMode,
          }
        }))
      })
    }

    if (!this.expand && this.value.length) {
      elements.push(h('span', {
        style: {
          display: undefined
        },
        class: {
          'jv-ellipsis': true,
        },
        on: {
          click: this.toggle
        },
        attrs: {
          title: `click to reveal ${this.value.length} hidden items`
        },
        domProps: {
          innerText: '...'
        }
      }))
    }

    elements.push(h('span', {
      class: {
        'jv-item': true,
        'jv-array': true,
      },
      domProps: {
        innerText: ']'
      }
    }))

    return h('span', elements)
  }
}
</script>
