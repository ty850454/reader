<template>
  <div class="reader">
    <div
      class="box"
      v-on:touchstart="touchstart"
      v-on:touchmove="touchmove"
      v-on:touchend="touchend"
      ref="boxZone"
    >
      <div class="header" v-bind:class="{ hide: !menuShow }">顶栏</div>
      <div class="title">第三十二章 啦啦啦</div>
      <div
        class="inner"
        v-html="book"
        :style="{'transform':`translateX(${translateX}px)`,'column-gap':`${columnGap}px`,'transition':`transform ${transitionDuration}s`}"
        ref="innerZone"
      ></div>
      <div class="footer" v-bind:class="{ hide: !menuShow }">工具栏</div>
    </div>
  </div>
</template>
<script>
import Hammer from "hammerjs";
export default {
  name: "Reader",
  data() {
    return {
      book: "asd",
      oldTranslateX: 0,
      translateX: 0,
      page: 1,
      fingerXX: 0,
      columnGap: 20,
      transitionDuration: 0,
      menuShow: false
    };
  },
  mounted() {
    this.$nextTick(function() {
      let _this = this;
      const boxZone = new Hammer(this.$refs.boxZone);
      boxZone.on("panmove", function(ev) {
        _this.transitionDuration = 0;
        _this.translateX = _this.oldTranslateX + ev.deltaX;
      });

      boxZone.on("panend pancancel", function(ev) {
        // console.log(ev.offsetDirection)
        if (ev.offsetDirection === Hammer.DIRECTION_RIGHT) {
          _this.prevPage();
        } else if (ev.offsetDirection === Hammer.DIRECTION_LEFT) {
          _this.nextPage();
        } else {
          console.log("无法识别的方向", ev.offsetDirection)
        }
        // // console.log("回", ev.velocityX);
        // if (_this.translateX > 0) {
        //   // console.log("查看上一章")
        // }
        // // 如果松开的速度快，则直接换页
        // if (Math.abs(ev.velocityX) > 0.1) {
        //   if (ev.velocityX > 0) {
        //     console.log("快速左滑");
        //     _this.prevPage();
        //   } else {
        //     console.log("快速右滑");
        //     _this.nextPage();
        //   }
        // } else {
        //   let w = _this.$refs.boxZone.clientWidth;
        //   let temp = _this.translateX % w;
        //   let f = -parseInt(w / 2.5);
        //   console.log(temp, f);
        //   if (temp < f) {
        //     // console.log("翻页");
        //     _this.nextPage();
        //   } else {
        //     // console.log("不翻页");
        //     _this.prevPage();
        //   }
        // }
      });

      boxZone.on("tap", function(ev) {
        let x = ev.center.x;
        let y = ev.center.y;
        let $boxZone = _this.$refs.boxZone;
        let w = $boxZone.clientWidth;
        let h = $boxZone.clientHeight;
        // console.log(x,w)
        if (x < w / 3) {
          _this.showPage(_this.calcPage() + 1,0);
        } else if (x > w - w / 3) {
          _this.nextPage(0);
        } else {
          _this.menuShow = !_this.menuShow;
        }
      });

      // boxZone.on("swipeleft", function(ev) {
      //   console.log("左滑", ev.velocityX)
      //    _this.nextPage();
      // });

      // boxZone.on("swiperight", function(ev) {
      //   _this.prevPage();
      // });

      this.$ajax.get("/book/1.txt").then(function(response) {
        _this.book = response.data.replace(/\r/g, "<br>");
      });
    });
  },
  methods: {
    touchstart(e) {
      // this.fingerXX = this.getFingerPercent((e.changedTouches[0].target.clientWidth - e.changedTouches[0].pageX) / e.changedTouches[0].target.clientWidth);
      // console.log(this.fingerXX);
    },
    touchmove(e) {
      // e.changedTouches[0].target.scrollLeft=100
      // let fingerX = this.getFingerPercent(e.changedTouches[0].pageX / e.changedTouches[0].target.clientWidth);
      // this.translateX = -(this.page * 100 - fingerX) + this.fingerXX;
      // console.log(e.changedTouches[0]);
    },
    touchend(e) {
      // console.log(this.translateX);
    },
    getFingerPercent(coord) {
      let fingerX = (coord * 100).toFixed(0);
      if (fingerX < -100) {
        fingerX = -100;
      } else if (fingerX > 100) {
        fingerX = 100;
      }
      return parseInt(fingerX);
    },
    prevPage(speed) {
      this.showPage(this.calcPage(), speed);
    },
    nextPage(speed) {
      this.showPage(this.calcPage() - 1, speed);
    },
    showPage(page,speed) {
      let $innerZone = this.$refs.innerZone;
      if (-this.translateX >= $innerZone.scrollWidth - parseInt($innerZone.clientWidth * 2)) {
        console.log("准备下一章");
      } else if (-this.translateX > $innerZone.scrollWidth - $innerZone.clientWidth) {
        console.log("下一章");
      }

      if (speed === undefined || speed === null) {
        this.transitionDuration = 0.3;
      } else {
        this.transitionDuration = speed;
      }
      
      this.translateX = page * (this.$refs.boxZone.clientWidth + this.columnGap - 20);
      this.oldTranslateX = this.translateX;
    },
    calcPage() {
      return parseInt(this.translateX / this.$refs.boxZone.clientWidth);
    }
  }
};
</script>

<style scoped>
.box {
  padding: 40px 10px 10px;
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  color: #888;
  background-color: #333;
  font-size: 20px;
}
.inner {
  padding: 0px;
  height: 100%;
  columns: calc(100vw - 32px) 1;
  transition: none;
}
.title {
  position: absolute;
  top: 12px;
  left: 10px;
  font-size: 15px;
}
.header {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 80px;
  background-color: black;
  color: white;
  z-index: 100;
  transition: top 0.3s;
}
.footer {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 80px;
  background-color: black;
  color: white;
  z-index: 100;
  transition: bottom 0.3s;
}
.footer.hide {
  bottom: -80px;
}
.header.hide {
  top: -80px;
}
</style>
