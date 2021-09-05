<template>
  <div class="hello">
    <div class="bigDiv" id="bigDiv" @mousedown="getDiv">
      <div
        v-for="(item, index) in List"
        :key="index"
        class="selectDiv"
        :style="{
          top: item.top,
          left: item.left,
          width: item.width,
          height: item.height,
          'line-height': item.height,
        }"
      >
        {{ item.name }}
      </div>
    </div>
    <div
      v-show="show"
      style="
        width: 100%;
        height: 100%;
        background: #00000050;
        position: fixed;
        top: 0;
      "
    >
      <div style="background:#fff;width:50%;height:50%">
        <input type="text" v-model="name" />
        <button @click="confirm">确定</button>
      </div>
    </div>
  </div>
</template>

<script>
// 基于准备好的dom，初始化echarts实例
export default {
  name: "HelloWorld",
  props: {},
  data() {
    return {
      show: false,
      name: "",
      scrollX: 0,
      scrollY: 0,
      startX: 0,
      startY: 0,
      initx: 0,
      inity: 0,
      top: 0,
      left: 0,
      width: 0,
      height: 0,
      List: [
        {
          name: "div1",
          top: "10px",
          left: "10px",
          width: "50px",
          height: "50px",
        },
      ],
      indexNow: 1,
    };
  },
  created() {},
  mounted() {},
  methods: {
    confirm() {
      this.List[this.indexNow].name = this.name;
      this.show = false;
    },
    getDiv(e) {
      //  创建选框节点
      this.scrollX =
        document.documentElement.scrollLeft || document.body.scrollLeft;
      this.scrollY =
        document.documentElement.scrollTop || document.body.scrollTop;
      //  设置选框的初始位置
      this.startX = e.x + this.scrollX || e.clientX + this.scrollX;
      this.startY = e.y + this.scrollY || e.clientY + this.scrollY;
      this.List.push({
        name: "",
        top: `${this.startY}px`,
        left: `${this.startX}px`,
      });
      //  清除事件冒泡、捕获
      this.indexNow = this.List.length - 1;
      this.clearBubble(e);
      document
        .getElementById("bigDiv")
        .addEventListener("mousemove", this.mousemovefn);
      document.body.addEventListener("mouseup", this.mouseUpfn);
    },
    mousemovefn(e) {
      //  设置选框可见
      //  根据鼠标移动，设置选框的位置、宽高
      this.initx = e.x + this.scrollX || e.clientX + this.scrollX;
      this.inity = e.y + this.scrollY || e.clientY + this.scrollY;
      //  暂存选框的位置及宽高，用于将 select-item 选中
      this.left = Math.min(this.initx, this.startX);
      this.top = Math.min(this.inity, this.startY);
      this.width = Math.abs(this.initx - this.startX);
      this.height = Math.abs(this.inity - this.startY);
      this.List[this.indexNow].left = `${this.left - 8}px`;//8是当前大div距离 左边的距离
      this.List[this.indexNow].top = `${this.top - 60}px`;//60是当前大div距离 上边的距离
      this.List[this.indexNow].width = `${this.width}px`;
      this.List[this.indexNow].height = `${this.height}px`;
      this.$forceUpdate();
      this.clearBubble(e);
    },
    clearBubble(e) {
      if (e.stopPropagation) {
        e.stopPropagation();
      } else {
        e.cancelBubble = true;
      }
      if (e.preventDefault) {
        e.preventDefault();
      } else {
        e.returnValue = false;
      }
    },
    mouseUpfn(e) {
      document
        .getElementById("bigDiv")
        .removeEventListener("mousemove", this.mousemovefn);
      this.show = true;

      this.clearBubble(e);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.bigDiv {
  width: 1200px;
  height: 600px;
  background: #ccc;
  position: relative;
}
.selectDiv {
  position: absolute;
  background: #ff000050;
  text-align: center;
}
</style>
