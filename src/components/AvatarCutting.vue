<!--
 * @Description: change_img_crop component
 * @Author: dizzyLee
 * @Date: 2019-06-13 10:00:54
 * @LastEditTime: 2019-06-18 09:00:38
 * @LastEditors: Please set LastEditors
 -->

<template>
  <div class="out_border" @mouseup="closeCropSizeChange()">
    <div class="left_body">
      <p class="body_title">裁剪头像</p>
      <!-- 裁剪部分 -->
      <div class="img_place_1" @mousemove="getCropSize();">
        <img class="img_1" id="img_1" :src="imgSrc" alt="头像">
        <img id="img_2" class="img_2" :src="imgSrc" alt="头像">
        <!-- 裁剪框 -->
        <div
          id="mainBox"
          @mousewheel="handleMouseWheel"
          @mouseup="closeIsDrag();"
          @mousedown="openIsDrag"
          @mousemove="getCoordinate()"
          @mouseout="closeIsDrag()"
        >
          <div id="left-up" @mousedown.stop="openCropSizeChange('left_up')" class="minBox left-up"></div>
          <div id="up" @mousedown.stop="openCropSizeChange('up')" class="minBox up"></div>
          <div id="right-up" @mousedown.stop="openCropSizeChange('up')" class="minBox right-up"></div>
          <div id="left" @mousedown.stop="openCropSizeChange('left')" class="minBox left"></div>
          <div id="right" @mousedown.stop="openCropSizeChange('right')" class="minBox right"></div>
          <div id="left-down" @mousedown.stop="openCropSizeChange('left')" class="minBox left-down"></div>
          <div id="down" @mousedown.stop="openCropSizeChange('bottom')" class="minBox down"></div>
          <div
            id="right-down"
            @mousedown.stop="openCropSizeChange('bottom')"
            class="minBox right-down"
          ></div>
        </div>
      </div>
    </div>
    <div class="mid_line"></div>
    <div class="right_body">
      <p class="body_title">预览</p>
      <!-- 预览部分 -->
      <canvas
        style="display:none;"
        width="64"
        height="64"
        id="myCanvas"
        class="avatar_pre"
      >Your browser does not support the HTML5 canvas tag.</canvas>
      <img :src="basesrc" style="width:64px;height:64px;border-radius:50%;" alt>
      <img :src="basesrc" style="width:48px;height:48px;border-radius:50%;" alt>
      <img :src="basesrc" style="width:32px;height:32px;border-radius:50%;" alt>
      <div class="upload" @click="toDataUrl">裁剪并上传</div>
      <div class="cancel">取消</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AvatarCutting",
  props: {
    imgSrc: String,
    change:[Object,Function]
  },
  data() {
    return {
      isDrag: false,
      isChangeSize: false,
      coordinateX: 0,
      coordinateY: 0,
      mainBoxX: 53,
      mainBoxY: 17,
      realMainBoxX: 53, //裁切框左上角的x坐标
      realMainBoxY: 17, //裁切框左上角的y坐标
      cropSize: 174, //裁切框的大小
      cropSizeXY: [0, 0], //获取鼠标坐标
      caiqiejuliRight: 0,
      overSizeW: 0, //超出的大小
      overSizeH: 0,
      basesrc: ""
    };
  },
  methods: {
    /**
     * @description: 鼠标滚轮放大缩小裁切框
     * @param {type} 
     * @return: 
     */
    handleMouseWheel(e) {
      let main_box = document.getElementById("mainBox")
      let sonRight = main_box.getBoundingClientRect()
        .right;
      let sonBottom = main_box.getBoundingClientRect()
        .bottom;
      let fatherRight = document.getElementById("img_2").getBoundingClientRect()
        .right;
      let fatherBottom = document
        .getElementById("img_2")
        .getBoundingClientRect().bottom;
      if (sonRight >= fatherRight + 1) {
        this.closeCropSizeChange();
        return
      } else if (sonBottom >= fatherBottom + 1) {
        this.closeCropSizeChange();
        return
      } else {
        if (e.deltaY > 0) {
          main_box.style.width =
            this.cropSize - 1 + "px";
          main_box.style.height =
            this.cropSize - 1 + "px";
          let main_box_width = window.getComputedStyle(main_box).height
          let left = window.getComputedStyle(main_box).left
          let height = window.getComputedStyle(main_box).top
          let right = parseInt(left) + parseInt(main_box_width)
          let bottom = parseInt(height) + parseInt(main_box_width)
          document.getElementById("img_2").style.clip =
            "rect(" + height + "," + right + "px," + bottom + "px," + left + ")";
          this.cropSize = this.cropSize - 1;
        } else if (e.deltaY < 0) {
          main_box.style.width =
            this.cropSize + 1 + "px";
          main_box.style.height =
            this.cropSize + 1 + "px";
          let main_box_width = window.getComputedStyle(main_box).height
          let left = window.getComputedStyle(main_box).left
          let height = window.getComputedStyle(main_box).top
          let right = parseInt(left) + parseInt(main_box_width)
          let bottom = parseInt(height) + parseInt(main_box_width)
          document.getElementById("img_2").style.clip =
            "rect(" + height + "," + right + "px," + bottom + "px," + left + ")";
          this.cropSize = this.cropSize + 1;
        }
      }
    },
    toDataUrl() {
      var canvas = document.getElementById("myCanvas");
      var dataURL = canvas.toDataURL();
      
      // console.log(dataURL);
    },
    openIsDrag(e) {
      this.isDrag = true;
      this.coordinateX = e.clientX;
      this.coordinateY = e.clientY;
    },
    openCropSizeChange(e) {
      console.log("open");
      this.cropSizeXY[0] = window.event.clientX;
      this.cropSizeXY[1] = window.event.clientY;
      this.isChangeSize = e;
      if ((e = "left")) {
        let a =
          document.getElementById("mainBox").getBoundingClientRect().left -
          document.getElementById("img_2").getBoundingClientRect().left;
        this.caiqiejuliRight = a;
      }
      if ((e = "up")) {
        let a =
          document.getElementById("mainBox").getBoundingClientRect().top -
          document.getElementById("img_2").getBoundingClientRect().top;
        this.caiqiejuliRight = a;
      }
      if ((e = "left_up")) {
        let top =
          document.getElementById("mainBox").getBoundingClientRect().top -
          document.getElementById("img_2").getBoundingClientRect().top;
        let left =
          document.getElementById("mainBox").getBoundingClientRect().left -
          document.getElementById("img_2").getBoundingClientRect().left;
        this.caiqiejuliRight = [top, left];
      }
    },
    closeCropSizeChange() {
      this.isChangeSize = false;
      this.cropSize = document.getElementById("mainBox").offsetHeight;
    },
    closeIsDrag() {
      this.isDrag = false;
      this.realMainBoxX = this.mainBoxX;
      this.realMainBoxY = this.mainBoxY;
      console.log("close");
    },
    /**
     * @description: 拖动裁切框逻辑
     * @param {type}
     * @return:
     */
    getCoordinate() {
      if (this.isDrag) {
        let x = window.event.clientX - this.coordinateX + this.realMainBoxX;
        let y = window.event.clientY - this.coordinateY + this.realMainBoxY;
        if (x <= 0) {
          this.mainBoxX = 0;
        } else if (x >= 280 - this.cropSize) {
          this.mainBoxX = 280 - this.cropSize;
        } else {
          this.mainBoxX =
            window.event.clientX - this.coordinateX + this.realMainBoxX;
        }
        if (y < 0) {
          this.mainBoxY = 0;
        } else if (y >= 280 - this.cropSize) {
          this.mainBoxY = 280 - this.cropSize;
        } else {
          this.mainBoxY =
            window.event.clientY - this.coordinateY + this.realMainBoxY;
        }
        let clipRight = this.mainBoxX + this.cropSize;
        let clipButtom = this.mainBoxY + this.cropSize;
        document.getElementById("mainBox").style.top = this.mainBoxY + "px";
        document.getElementById("mainBox").style.left = this.mainBoxX + "px";
        document.getElementById("img_2").style.clip =
          "rect(" +
          this.mainBoxY +
          "px," +
          clipRight +
          "px," +
          clipButtom +
          "px," +
          this.mainBoxX +
          "px)";
      }
    },
    /**
     * @description: 裁切逻辑
     * @param {type}
     * @return:
     */
    getCropSize() {
      if (this.isChangeSize == "right") {
        let x = window.event.clientX - this.cropSizeXY[0] + this.cropSize;
        let y = window.event.clientY - this.cropSizeXY[1] + this.cropSize;
        let clipRight = x + this.cropSize;
        let clipButtom = y + this.cropSize;
        let sonRight = document
          .getElementById("mainBox")
          .getBoundingClientRect().right;
        let sonBottom = document
          .getElementById("mainBox")
          .getBoundingClientRect().bottom;
        let fatherRight = document
          .getElementById("img_2")
          .getBoundingClientRect().right;
        let fatherBottom = document
          .getElementById("img_2")
          .getBoundingClientRect().bottom;
        let top =
          document.getElementById("mainBox").getBoundingClientRect().top -
          document.getElementById("img_2").getBoundingClientRect().top;
        let left =
          document.getElementById("mainBox").getBoundingClientRect().left -
          document.getElementById("img_2").getBoundingClientRect().left;
        let right = left + x;
        let bottom = top + x;
        if (sonRight >= fatherRight + 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        } else if (sonBottom >= fatherBottom + 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        }
        document.getElementById("mainBox").style.width = x + "px";
        document.getElementById("mainBox").style.height = x + "px";
        document.getElementById("img_2").style.clip =
          "rect(" + top + "px," + right + "px," + bottom + "px," + left + "px)";
      } else if (this.isChangeSize == "left") {
        let x = this.cropSizeXY[0] - window.event.clientX + this.cropSize;
        let y = this.cropSizeXY[1] - window.event.clientY + this.cropSize;
        let clipRight = x + this.cropSize;
        let clipButtom = y + this.cropSize;
        let sonRight = document
          .getElementById("mainBox")
          .getBoundingClientRect().right;
        let sonBottom = document
          .getElementById("mainBox")
          .getBoundingClientRect().bottom;
        let fatherRight = document
          .getElementById("img_2")
          .getBoundingClientRect().right;
        let fatherBottom = document
          .getElementById("img_2")
          .getBoundingClientRect().bottom;
        let top =
          document.getElementById("mainBox").getBoundingClientRect().top -
          document.getElementById("img_2").getBoundingClientRect().top;
        let left =
          document.getElementById("mainBox").getBoundingClientRect().left -
          document.getElementById("img_2").getBoundingClientRect().left;
        let right = left + x;
        let bottom = top + x;
        if (sonRight >= fatherRight - 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        } else if (sonBottom >= fatherBottom - 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        }
        let a =
          this.caiqiejuliRight[1] - this.cropSizeXY[0] + window.event.clientX;
        this.mainBoxX = left;
        this.mainBoxY = top;
        document.getElementById("mainBox").style.left = a + "px";
        document.getElementById("mainBox").style.width = x + "px";
        document.getElementById("mainBox").style.height = x + "px";
        document.getElementById("img_2").style.clip =
          "rect(" + top + "px," + right + "px," + bottom + "px," + left + "px)";
      } else if (this.isChangeSize == "bottom") {
        let x = window.event.clientX - this.cropSizeXY[0] + this.cropSize;
        let y = window.event.clientY - this.cropSizeXY[1] + this.cropSize;
        let clipRight = x + this.cropSize;
        let clipButtom = y + this.cropSize;
        let sonRight = document
          .getElementById("mainBox")
          .getBoundingClientRect().right;
        let sonBottom = document
          .getElementById("mainBox")
          .getBoundingClientRect().bottom;
        let fatherRight = document
          .getElementById("img_2")
          .getBoundingClientRect().right;
        let fatherBottom = document
          .getElementById("img_2")
          .getBoundingClientRect().bottom;
        let top =
          document.getElementById("mainBox").getBoundingClientRect().top -
          document.getElementById("img_2").getBoundingClientRect().top;
        let left =
          document.getElementById("mainBox").getBoundingClientRect().left -
          document.getElementById("img_2").getBoundingClientRect().left;
        let right = left + y;
        let bottom = top + y;
        if (sonRight >= fatherRight + 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        } else if (sonBottom >= fatherBottom + 1) {
          this.closeCropSizeChange();
          console.log(sonBottom, fatherBottom);
        }
        document.getElementById("mainBox").style.width = y + "px";
        document.getElementById("mainBox").style.height = y + "px";
        document.getElementById("img_2").style.clip =
          "rect(" + top + "px," + right + "px," + bottom + "px," + left + "px)";
      } else if (this.isChangeSize == "up") {
        let x = this.cropSizeXY[0] - window.event.clientX + this.cropSize;
        let y = this.cropSizeXY[1] - window.event.clientY + this.cropSize;
        let clipRight = x + this.cropSize;
        let clipButtom = y + this.cropSize;
        let sonRight = document
          .getElementById("mainBox")
          .getBoundingClientRect().right;
        let sonBottom = document
          .getElementById("mainBox")
          .getBoundingClientRect().bottom;
        let fatherRight = document
          .getElementById("img_2")
          .getBoundingClientRect().right;
        let fatherBottom = document
          .getElementById("img_2")
          .getBoundingClientRect().bottom;
        let top =
          document.getElementById("mainBox").getBoundingClientRect().top -
          document.getElementById("img_2").getBoundingClientRect().top;
        let left =
          document.getElementById("mainBox").getBoundingClientRect().left -
          document.getElementById("img_2").getBoundingClientRect().left;
        let right = left + y;
        let bottom = top + y;
        if (sonRight >= fatherRight - 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        } else if (sonBottom >= fatherBottom - 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        }
        let a =
          this.caiqiejuliRight[0] + window.event.clientY - this.cropSizeXY[1];
        this.mainBoxX = left;
        this.mainBoxY = top;
        document.getElementById("mainBox").style.top = a + "px";
        document.getElementById("mainBox").style.width = y + "px";
        document.getElementById("mainBox").style.height = y + "px";
        document.getElementById("img_2").style.clip =
          "rect(" + top + "px," + right + "px," + bottom + "px," + left + "px)";
      } else if (this.isChangeSize == "left_up") {
        let x = this.cropSizeXY[0] - window.event.clientX + this.cropSize;
        let y = this.cropSizeXY[1] - window.event.clientY + this.cropSize;
        let clipRight = x + this.cropSize;
        let clipButtom = y + this.cropSize;
        let sonRight = document
          .getElementById("mainBox")
          .getBoundingClientRect().right;
        let sonBottom = document
          .getElementById("mainBox")
          .getBoundingClientRect().bottom;
        let fatherRight = document
          .getElementById("img_2")
          .getBoundingClientRect().right;
        let fatherBottom = document
          .getElementById("img_2")
          .getBoundingClientRect().bottom;
        let top =
          document.getElementById("mainBox").getBoundingClientRect().top -
          document.getElementById("img_2").getBoundingClientRect().top;
        let left =
          document.getElementById("mainBox").getBoundingClientRect().left -
          document.getElementById("img_2").getBoundingClientRect().left;
        let right = left + y;
        let bottom = top + y;
        if (sonRight >= fatherRight - 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        } else if (sonBottom >= fatherBottom - 1) {
          this.closeCropSizeChange();
          console.log(sonRight, fatherRight);
        }
        let a =
          this.caiqiejuliRight[0] + window.event.clientY - this.cropSizeXY[1];
        let b =
          this.caiqiejuliRight[1] - this.cropSizeXY[0] + window.event.clientX;
        this.mainBoxX = left;
        this.mainBoxY = top;
        document.getElementById("mainBox").style.top = a + "px";
        document.getElementById("mainBox").style.left = b + "px";
        document.getElementById("mainBox").style.width = y + "px";
        document.getElementById("mainBox").style.height = y + "px";
        document.getElementById("img_2").style.clip =
          "rect(" + top + "px," + right + "px," + bottom + "px," + left + "px)";
      }
    },
    drawImage() {
      var c = document.getElementById("myCanvas");
      var ctx = c.getContext("2d");
      var img = document.getElementById("img_1");
      let image = new Image();
      image.src = img.src;
      image.crossOrigin = "anonymous";
      if (image.width > image.height) {
        let w = image.width / 2 - 140;
        this.overSizeW = this.mainBoxX + w;
        this.overSizeH = this.mainBoxY;
      } else if (image.height > image.width) {
        let h = image.height / 2 - 140;
        this.overSizeW = this.mainBoxX;
        this.overSizeH = this.mainBoxY + h;
      } else {
        this.overSizeW = this.mainBoxX;
        this.overSizeH = this.mainBoxY;
      }
      ctx.drawImage(
        image,
        this.overSizeW,
        this.overSizeH,
        this.cropSize,
        this.cropSize,
        0,
        0,
        64,
        64
      );
      var canvas = document.getElementById("myCanvas");
      var dataURL = canvas.toDataURL();
      this.basesrc = dataURL;
      // console.log(dataURL);

      //   ctx.drawImage(image, this.overSizeW, this.overSizeH, this.cropSize, this.cropSize, 80, 16, 48, 48);
      //   ctx.drawImage(image, this.overSizeW, this.overSizeH, this.cropSize, this.cropSize, 144, 32, 32, 32);
    }
  },
  watch: {
    cropSize: "drawImage",
    realMainBoxX: "drawImage",
    realMainBoxY: "drawImage"
  },
  mounted: function() {
    var c = document.getElementById("myCanvas");
    var ctx = c.getContext("2d");
    ctx.strokeStyle = "rgb(255,255,255)";
    ctx.arc(32, 32, 32, 0, 2 * Math.PI);
    // ctx.arc(104,40,24,0,2*Math.PI);
    // ctx.arc(160,48,16,0,2*Math.PI);
    ctx.stroke();
    ctx.clip();
    var img = document.getElementById("img_1");
    let image = new Image();
    image.src = img.src;
    image.crossOrigin = "anonymous";
    if (image.width > image.height) {
      let w = image.width / 2 - 140;
      this.overSizeW = this.mainBoxX + w;
      this.overSizeH = this.mainBoxY;
    } else if (image.height > image.width) {
      let h = image.height / 2 - 140;
      this.overSizeW = this.mainBoxX;
      this.overSizeH = this.mainBoxY + h;
    } else {
      this.overSizeW = this.mainBoxX;
      this.overSizeH = this.mainBoxY;
    }
    ctx.drawImage(
      image,
      this.overSizeW,
      this.overSizeH,
      this.cropSize,
      this.cropSize,
      0,
      0,
      64,
      64
    );
    var canvas = document.getElementById("myCanvas");
    var dataURL = canvas.toDataURL();
    // console.log(dataURL);
    this.basesrc = dataURL;
    // ctx.drawImage(image, this.overSizeW, this.overSizeH, this.cropSize, this.cropSize, 80, 16, 48, 48);
    // ctx.drawImage(image, this.overSizeW, this.overSizeH, this.cropSize, this.cropSize, 144, 32, 32, 32);
  }
};
</script>

<style scoped>
.out_border {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  margin: 0 auto;
  padding: 0px 32px 0px 32px;
  width: 520px;
  height: 382px;
  background: #fff;
  box-shadow: 0 15px 60px 0 rgba(0, 0, 0, 0.07);
  border-radius: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.left_body {
  width: 280px;
  height: 318px;
}
.body_title {
  margin: 0px 0px 24px 0px;
  font-family: PingFangSC-Semibold;
  font-size: 14px;
  color: #444444;
  letter-spacing: 0;
  line-height: 14px;
}
.img_place_1 {
  width: 280px;
  height: 280px;
  background: rgba(0, 0, 0, 0.19);
  border-radius: 10px;
  position: relative;
}
.img_1 {
  opacity: 0.5;
  width: 280px;
  height: 280px;
  border-radius: 10px;
  object-fit: cover;
  position: absolute;
  top: 0;
  left: 0;
  -webkit-user-drag: none;
}
.img_2 {
  width: 280px;
  height: 280px;
  border-radius: 10px;
  object-fit: cover;
  position: absolute;
  top: 0;
  left: 0;
  clip: rect(17px, 227px, 191px, 53px);
  -webkit-user-drag: none;
}
#mainBox {
  box-sizing: border-box;
  border: 2px solid #03a9f4;
  position: absolute;
  top: 17px;
  left: 53px;
  width: 174px;
  height: 174px;
  cursor: move;
}
.minBox {
  position: absolute;
  height: 10px;
  width: 10px;
  background-color: #03a9f4;
  border-radius: 50%;
}

.left-up {
  top: -6px;
  left: -6px;
  cursor: nw-resize;
}

.up {
  left: 50%;
  margin-left: -6px;
  top: -6px;
  cursor: n-resize;
}

.right-up {
  right: -6px;
  top: -6px;
  cursor: ne-resize;
}

.left {
  top: 50%;
  margin-top: -6px;
  left: -6px;
  cursor: w-resize;
}

.right {
  top: 50%;
  margin-top: -6px;
  right: -6px;
  cursor: w-resize;
}

.left-down {
  bottom: -6px;
  left: -6px;
  cursor: sw-resize;
}

.down {
  bottom: -6px;
  left: 50%;
  margin-left: -6px;
  cursor: s-resize;
}

.right-down {
  bottom: -6px;
  right: -6px;
  cursor: se-resize;
}
.mid_line {
  width: 1px;
  height: 280px;
  background: #e8e8e8;
  border: 0;
}
.right_body {
  width: 176px;
  height: 318px;
}
.avatar_pre {
  /* width: 176px;
  height: 64px; */
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
}
.avatar_large {
  width: 64px;
  height: 64px;
  border-radius: 50%;
  background: #cfcfcf;
  object-fit: cover;
}
.avatar_middle {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: #cfcfcf;
  object-fit: cover;
}
.avatar_small {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: #cfcfcf;
  object-fit: cover;
}
.upload {
  cursor: pointer;
  margin: 132px 0px 8px 0px;
  font-family: PingFangSC-Semibold;
  font-size: 14px;
  color: #ffffff;
  letter-spacing: 0;
  text-align: center;
  line-height: 38px;
  width: 176px;
  height: 38px;
  background: #03a9f4;
  border-radius: 4px;
}
.upload:hover {
  opacity: 0.8;
}
.cancel {
  cursor: pointer;
  font-family: PingFangSC-Semibold;
  font-size: 14px;
  color: #03a9f4;
  letter-spacing: 0;
  text-align: center;
  line-height: 38px;
  width: 176px;
  height: 38px;
  background: #fff;
  border-radius: 4px;
}
.cancel:hover {
  opacity: 0.8;
  background: #444;
}
</style>
