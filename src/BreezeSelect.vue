<template>
<div class="breeze-select-box"
  :style="{width: width + 'px'}">
  <div class="selected"
    @click="showOptionsList">
      <span>{{ selectedText }}</span>
    </div>
  <transition
    name="fadeIn"
    enter-active-class="animated fadeIn"
    leave-active-class="animated fadeOut">
    <div class="list"
      v-show="isShow">
      <input class="search" type="text"
        v-if="search"
        v-model="searchText"
        :placeholder="placeholderSearch"
        @input="searchOption">
      <div class="inner">
        <div class="item" v-if="multiple && selectAllText !== ''">
          <span>
            <input id="all" type="checkbox"
              :checked="selectedAllStatus"
              @change="selectAll">
            <label for="all">全选</label>
          </span>
        </div>
        <div class="item"
          v-for="(item, index) in items"
          :class="{selected: item.selectStatus && !multiple}"
          @click="selectItem(item)">
            <span v-if="multiple">
              {{setId(item)}}
              <input type="checkbox"
                :id="item.id"
                :value="item.text"
                v-model="selectedOptions"
                @change="selectItems">
              <label :for="item.id">{{ item.text }}</label>
            </span>
            <span v-else>
              {{ item.text }}
            </span>
        </div>
      </div>
    </div>
  </transition>
</div>
</template>

<script>
var breezeSelectId = 1;
export default {
  name: 'BreezeSelect',
  data() {
    return {
      selectedValue: '',
      selectedText: '',
      selectedOptions: [],
      searchText: '',
      items: [],
      selectedAllStatus: false,
      isShow: false
    }
  },
  props: {
    data: {
      type: Array,
      default: []
    },
    width: {
      type: Number,
      default: 120
    },
    placeholder: {
      type: String,
      default: '请选择'
    },
    placeholderSearch: {
      type: String,
      default: '查找内容'
    },
    selectAllText: {
      type: String,
      default: ''
    },
    search: {
      type: Boolean,
      default: false
    },
    multiple: {
      type: Boolean,
      default: false
    },
    callbackChange: {
      type: Function,
      default: () => {}
    }
  },
  watch: {
    data: {
      immediate: true,
      handler(val) {
        var arr = [];
        this.items = this.data.slice(0);
        this.items.forEach(item => {
          if (item.selectStatus) {
            arr.push(item.text);
          }
        });
        this.selectedOptions = arr;
      }
    },
    selectedOptions: {
      immediate: true,
      handler(val) {
        if (this.multiple) {
          if (this.selectedOptions.length === this.data.length && this.selectAllText !== '') {
            this.selectedText = this.selectAllText;
            this.selectedAllStatus = true;
          } else if (this.selectedOptions.length === 0) {
            this.selectedText = this.placeholder;
          } else {
            this.selectedText = this.selectedOptions.join(',');
            this.selectedAllStatus = false;
          }
        } else {
          this.selectedText = this.placeholder;
        }
      }
    }
  },
  mounted () {
    document.addEventListener('click', this.hideOptionsList, false)
  },
  destroyed () {
    document.removeEventListener('click', this.hideOptionsList, false)
  },
  methods: {
    setId: function(item){
      if (item.id === undefined) {
        item.id = 'breeze-select-item' + breezeSelectId++;
      }
    },
    showOptionsList: function(){
      if (this.search) {
        this.searchOption();
      }
      this.isShow = !this.isShow;
    },
    hideOptionsList: function(evt){
      if (!this.$el.contains(evt.target)) {
        this.isShow = false;
      }
    },
    selectItem: function(item){
      if (!this.multiple) {
        this.data.forEach(_item => {
          if (_item.text === item.text) {
            _item.selectStatus = true;
          } else {
            _item.selectStatus = false;
          }
        });
        this.selectedText = item.text;
        this.selectedValue = item.value;
        this.$nextTick(() => {
          this.isShow = false;
          this.callbackChange(this.selectedValue);
        });
      }
    },
    selectItems: function() {
      var arrValue = [];
      this.selectedOptions.forEach(item => {
        this.data.forEach(_item => {
          if (item === _item.text) {
            arrValue.push(_item.value);
          }
        });
      });
      this.callbackChange(arrValue);
    },
    selectAll: function(){
      var arrValue = [],
          arrText = [];
      if (!this.selectedAllStatus) {
        this.data.forEach(item => {
          arrText.push(item.text);
          arrValue.push(item.value);
        });
        this.selectedAllStatus = true;
      } else {
        this.selectedAllStatus = false;
      }
      this.selectedOptions = arrText;
      this.callbackChange(arrValue);
    },
    searchOption: function(){
      var searchReg = new RegExp(this.searchText.trim(), 'i'),
          arr = [];
      this.data.forEach((item, index) => {
        if (searchReg.test(item.text)) {
          var obj = {};
          obj.text = item.text;
          obj.value = item.value;
          obj.selectStatus = item.selectStatus;
          arr.push(obj);
        }
      });
      this.items = arr;
    }
  }
}
</script>

<style>
.breeze-select-box {
  position: relative;
  min-width: 120px;
  max-width: 300px;
  font-size: 10px;
  font-family: inherit;
  display: inline-block;
}

.breeze-select-box>.selected {
  position: relative;
  height: 28px;
  line-height: 28px;
  border-radius: 3px;
  border: 1px solid #999;
  font-size: 1.2em;
  padding: 0 35px 0 10px;
  color: #4C5565;
  cursor: pointer;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.breeze-select-box>.selected:before {
  position: absolute;
  top: 50%;
  right: 10px;
  width: 0;
  height: 0;
  margin-top: -4px;
  content: "";
  border-top: 8px solid #999;
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
}
.breeze-select-box>.list {
  position: absolute;
  top: 35px;
  left: 0px;
  min-width: 120px;
  max-width: 300px;
  font-size: 10px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
  background: #fff;
  border-radius: 3px;
  z-index: 100;
}
.breeze-select-box>.list>.search {
  width: 90%;
  height: 30px;
  margin: 5px auto 0;
  padding: 2px 5px;
  box-sizing: border-box;
  display: block;
}
.breeze-select-box>.list>.inner {
  padding: 5px;
  max-height: 200px;
  overflow: scroll;
}
.breeze-select-box>.list .item {
  max-width: 280px;
  height: 30px;
  line-height: 30px;
  padding: 0 10px;
  text-overflow: ellipsis;
  white-space: nowrap;
  cursor: pointer;
  overflow: hidden;
}
.breeze-select-box>.list .item:hover {
  background-color: #eee;
  border-radius: 3px;
}
.breeze-select-box>.list .item.selected {
  background: #333;
  border-radius: 3px;
  color: #fff;
}
.breeze-select-box>.list .item>span>input {
  position: absolute;
  width: 0;
  height: 0;
  opacity: 0;
}
.breeze-select-box>.list .item>span>label {
  position: relative;
  width: 100%;
  height: 30px;
  padding-left: 20px;
  line-height: 30px;
  display: inline-block;
}
.breeze-select-box>.list .item>span>label:before {
  position: absolute;
  top: 50%;
  left: 0;
  width: 11px;
  height: 11px;
  margin-top: -6px;
  content: "";
  border: 1px solid #ccc;
  background-color: #fff;
  border-radius: 2px;
  display: block;
}
.breeze-select-box>.list .item>span>label:after {
  position: absolute;
  top: 50%;
  left: 2px;
  width: 7px;
  height: 3px;
  margin-top: -3px;
  content: "";
  border-left: 2px solid #fff;
  border-bottom: 2px solid #fff;
  background-color: transparent;
  transform: rotate(-45deg);
  border-radius: 2px;
  display: block;
}
.breeze-select-box>.list .item>span> input:checked + label:before {
  border: 1px solid #1e9fff;
  background-color: #1e9fff;
}
.breeze-select-box .animated {
  animation-duration: 0.5s;
  animation-fill-mode: both;
}
.breeze-select-box .fadeIn {
  animation-name: fadeIn;
}
.breeze-select-box .fadeOut {
  animation-name: fadeOut;
}
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
@keyframes fadeOut {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}
</style>
