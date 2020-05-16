<template>
  <div id="cascader" ref="cascader" :class="{'on':isVisibleFirst}">
    <div class="select-box" @click="showFirstLayer">
      <div class="curr" :class="{'on':isVisibleFirst}">
        <span class="arrow"></span>
        <div v-for="(item,index) in labelList" :key="index">
          <span>{{item}}</span>
          <span style="margin-left: 10px;" @click="deleteItem(index)">X</span>
        </div>
      </div>
      <div class="list-box clearfix" v-if="isVisibleFirst">
        <div class="list">
          <div
            class="item"
            v-for="(theitem,index) in list"
            @click.stop="givenValueOfCurr(index)"
            :class="{'active':index==curr}"
            :key="index"
          >
            {{theitem.name}}
            <span style="float:right;margin-right:20px;">></span>
          </div>
        </div>
        <div class="list" v-if="isVisibleSecond">
          <div
            class="item"
            v-for="(theSeItem,index1) in list[curr].children"
            @click="clickingSecondLayer(index1)"
            :class="{'active':index1==currSec}"
            :key="index1"
          >{{theSeItem.name}}</div>
        </div>
        <div class="icon"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    list: {
      type: Array,
      default: function() {
        return [];
      }
    }
  },
  data() {
    return {
      curr: -1,
      currSec: -1,
      firstTimeClick: 0,
      isVisibleFirst: false,
      isVisibleSecond: false,
      selectionBoxDisplay: true,
      chosenList: [],
      secList: []
    };
  },
  computed: {
    labelList() {
      return this.chosenList.map(item => {
        if (item.length) {
          return (
            this.list[item[0]].name +
            "/" +
            this.list[item[0]].children[item[1]].name
          );
        } else {
          return this.list[item].name;
        }
      });
    },
    valueList(){
        return this.chosenList.map( item => {
            if(item.length){
                return [this.list[item[0]].value,this.list[item[0]].children[item[1]].value]
            }else{
                return this.list[item].value;
            }
        })
    }
  },
  created() {
    window.addEventListener("click", e => {//点击cascader以外的地方，将两个下拉框隐藏
        if(!this.$refs.cascader.contains(e.target)){
            this.isVisibleFirst = false;
            this.isVisibleSecond = false;
        }
    });
  },
  watch: {
    firstTimeClick: function(val) {
      if (val === 0) {
        this.isVisibleFirst = false;
        this.isVisibleSecond = false;
      }
    }
  },
  methods: {
    showFirstLayer() {
      this.isVisibleFirst = !this.isVisibleFirst;
      if (this.firstTimeClick > 0) {
        this.isVisibleSecond = true;
      }
      this.chosenList.indexOf(this.curr);
      this.selectionBoxDisplay = false;
    },
    givenValueOfCurr(index) {
      this.curr = index;
      this.firstTimeClick += 1;
      if (this.firstTimeClick > 0) {
        this.isVisibleSecond = true;
      }
      if (this.chosenList.indexOf(this.curr) == -1) {
        this.chosenList.push(this.curr);
      }
      this.currSec = -1;
      this.$emit('onClick',this.valueList)
    },
    clickingSecondLayer(index1) {
      let i = this.chosenList.indexOf(this.curr);
      let value = this.list[this.curr].children[index1].value;
      if (this.secList.indexOf(value) == -1) {
        this.secList.push(value);
      } else {
        this.isVisibleSecond = false;
        event.stopPropagation();
        this.isVisibleFirst = false;
        return false;
      }
      if (i !== -1) {
        this.chosenList.splice(i, 1);
      }
      this.isVisibleFirst = false;
      this.currSec = index1;
      this.isVisibleSecond = false;
      event.stopPropagation();
      let addList = Object.assign([], [this.curr, this.currSec]);
      this.chosenList.push(addList);
      this.$emit('onClick',this.valueList)
    },
    deleteItem(index) {
      let chosenEle = this.chosenList[index];
      if (chosenEle.length) {
        this.secList.splice(
          this.secList.indexOf(
            this.list[chosenEle[0]].children[chosenEle[1]].value
          ),
          1
        );
      }
      this.labelList.splice(index, 1);
      this.chosenList.splice(index, 1);
      event.stopPropagation();
      if (this.labelList.length === 0) {
        this.firstTimeClick = 0;
      }
      this.$emit('onClick',this.valueList);
    }
  }
};
</script>

<style scoped lang="scss">
a {
  text-decoration: none;
}

.clearfix:before,
.clearfix:after {
  content: " ";
  display: table;
}

.clearfix:after {
  clear: both;
}

input {
  outline: none;
}

ol,
ul {
  list-style: none;
}

.select-box {
  position: relative;
  width: 400px;
}

.select-box .curr {
  width: 240px;
  padding-left: 15px;
  padding-right: 30px;
  border: 1px solid #ddd;
  border-radius: 5px;
  min-height: 40px;
  line-height: 40px;
  font-size: 14px;
  position: relative;
  z-index: 15;
  background-color: white;
}

.select-box .curr:hover {
  border-color: #aaa;
  cursor: pointer;
}

.arrow {
  position: absolute;
  right: 10px;
  top: 50%;
  display: block;
  width: 10px;
  height: 10px;
  border-left: 1px solid #aaa;
  border-top: 1px solid #aaa;
  transform: rotate(225deg);
  transform-origin: 2px 2px;
  transition: 200ms;
}
.arrow:hover {
  display: none;
}
.on .arrow {
  transform: rotate(45deg);
}

.list-box {
  position: absolute;
  left: 0;
  border: 1px solid #ddd;
  box-shadow: 0px 0px 8px 0px #ccc;
  padding: 5px 0;
  z-index: 10;
  transition: 200ms;
  background-color: #fff;
}

.on .list-box {
  /* top: 410px; */
  margin-top: 20px;
}

.list-box .list {
  float: left;
  width: 180px;
  height: 204px;
  overflow: auto;
  border-left: 1px solid #ccc;
}

.list-box .list.first {
  border: none;
}

.list .item {
  height: 38px;
  line-height: 38px;
  padding-left: 15px;
  font-size: 14px;
  cursor: pointer;
  position: relative;
}

.list .item:hover {
  background-color: rgb(245, 247, 250);
}

.list .item.active {
  color: #409eff;
  font-weight: bold;
}

.list .icon {
  position: absolute;
  top: -6px;
  left: 30px;
  width: 10px;
  height: 10px;
  border-left: 1px solid #ddd;
  border-top: 1px solid #ddd;
  z-index: 20;
  transform: rotate(45deg);
  background-color: white;
}

.list .arrow-icon {
  position: absolute;
  right: 10px;
  top: 17px;
  display: block;
  width: 10px;
  height: 10px;
  border-left: 1px solid #aaa;
  border-top: 1px solid #aaa;
  transform: rotate(135deg);
  transform-origin: 2px 2px;
  transition: 200ms;
}
</style>
