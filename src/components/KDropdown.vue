<template>
  <div class="k-dropdown-container">
    <div class="k-dropdown-sub-container">
      <div
        class="k-dropdown-outer"
        @click="toggleShowItems"
        ref="dropdownOuter"
      >
        <div class="k-dropdown" :class="{ 'k-dropdown-disable': disabled }">
          <div class="k-dropdown-selected">
            <span class="k-dropdown-selected-name">
              <span class="k-dropdown-selected-label">{{ selected }}</span>
              <span class="icon-arrow-down">
                <svg>
                  <path :d="mdiChevronDown" />
                </svg>
              </span>
            </span>
          </div>
        </div>
      </div>
      <div :style="[displayStyle, listHeightStyle]" class="k-list-outer">
        <k-item
          v-for="(item, i) in items"
          :key="i"
          :selected="value === item.value"
          :label="item.label"
          :value="item.value"
          @click="onClickItem"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { mdiChevronDown } from '@mdi/js';
import KItem from './KItem.vue';

export default {
  components: {
    KItem
  },

  props: {
    items: {
      type: Array,
      defaut: () => [],
      required: true
    },
    value: {
      type: String,
      defaut: null,
      required: false
    },
    disabled: {
      type: Boolean,
      defaut: false,
      required: false
    }
  },

  data() {
    return {
      mdiChevronDown,
      visibleItems: false,
      listHeight: 400
    };
  },

  computed: {
    selected() {
      let index = -1;
      this.items &&
        this.items.forEach((item, i) => {
          if (item.value === this.value) {
            index = i;
          }
        });

      if (index === -1) {
        return '';
      }
      return this.items[index].label;
    },

    displayStyle() {
      return this.visibleItems && !this.disabled
        ? { display: 'block' }
        : { display: 'none' };
    },

    listHeightStyle() {
      return {
        'max-height': this.listHeight + 'px'
      };
    }
  },

  methods: {
    toggleShowItems() {
      this.visibleItems = !this.visibleItems;
    },

    close() {
      this.visibleItems = false;
    },

    onClickItem(value) {
      this.$emit('change', value);
      this.$emit('input', value);
      this.visibleItems = false;
    }
  },

  watch: {
    visibleItems(current, old) {
      if (old === false) {
        setTimeout(() => {
          document.addEventListener('click', this.close, false);
        }, 250);
      } else {
        document.removeEventListener('click', this.close, false);
      }
      this.listHeight =
        document.documentElement.clientHeight -
        this.$refs.dropdownOuter.getBoundingClientRect().bottom -
        24;
    }
  }
};
</script>

<style scoped>
.k-dropdown-container,
.k-dropdown-sub-container,
.k-dropdown-outer {
  display: inline-block;
  height: 40px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
.k-dropdown-container {
  padding: 4px 0;
}
.k-dropdown {
  position: relative;
  display: inline-block;
  overflow: hidden;
  box-sizing: border-box;
  border: 1px solid #e3e7e8;
  background-color: #f7f9fa;
  box-shadow: 1px 1px 1px #fff inset;
  color: #3498db;
  text-overflow: ellipsis;
}
.k-dropdown:hover {
  background-color: #f4f7f8;
  cursor: pointer;
}
.k-dropdown.k-dropdown-disable,
.k-dropdown.k-dropdown-disable:hover {
  cursor: not-allowed;
  border: none;
  background-color: #dbdcdd;
}
.k-dropdown.k-dropdown-disable .k-dropdown-selected-name {
  color: #888;
}
.k-dropdown-selected {
  width: 139px;
  height: 38px;
  padding-right: 39px;
}
.k-dropdown-selected-name {
  color: #3498db;
  font-size: 14px;
  line-height: 38px;
}
.k-dropdown-selected-label {
  padding-left: 24px;
  width: 114px;
  height: 38px;
  display: inline-block;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.k-dropdown-selected-name .icon-arrow-down {
  position: absolute;
}
.k-dropdown-selected-name .icon-arrow-down svg {
  width: 24px;
  height: 24px;
  padding: 0 7px;
  vertical-align: middle;
}
.k-dropdown-selected-name .icon-arrow-down svg path {
  fill: #3498db;
}
.k-dropdown.k-dropdown-disable
  .k-dropdown-selected-name
  .icon-arrow-down
  svg
  path {
  fill: #888;
}
.k-dropdown-sub-container .k-list-outer {
  position: absolute;
  overflow-y: auto;
  min-width: 280px;
  z-index: 2000;
  padding: 8px 0;
}
.k-dropdown-sub-container .k-list-outer .k-list-item {
  padding: 8px 16px 8px 25px;
}
.k-dropdown-sub-container .k-list-outer .k-list-item-selected {
  padding: 8px 16px 8px 8px;
}

.k-list-outer {
  margin-top: -6px;
  padding: 12px 0 0 0;
  border: 1px solid #e3e7e8;
  background-color: #fff;
  box-shadow: 1px 1px 12px #f5f5f5 inset, -1px -1px 12px #f5f5f5 inset;
  line-height: 1;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>
