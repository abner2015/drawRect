<template>
  <div class="container">
    <!-- Swiper -->
     <div class="swiper-container gallery-top">
      <!-- <div class="swiper-wrapper first-wrapper">
        <div
          class="swiper-slide"
          v-for="item in banners"
          :style="'background-image:url('+item.url+'); background-size: 400px 300px; background-repeat:no-repeat;'"
          :key="item.url"
        ></div>
      </div> -->
    </div>
    <!-- <div style="display:flex; ">
      
    </div> -->
    <canvas id="canvas" ref="canvas" width="400" height="240" >这是浏览器不支持canvas时展示的信息</canvas>
    <div id="contextmenu-output"></div>
    <img id="img" src="../../images/avator.jpg" style="display: none;" />

    <!-- Add Arrows -->
    <div class="swiper-button-next swiper-button-white"></div>
    <div class="swiper-button-prev swiper-button-white"></div>
    <div class="swiper-container gallery-thumbs">
      <div class="swiper-wrapper">
        <div
          class="swiper-slide"
          v-for="item in banners"
          :style="'background-image:url('+item.url+');'"
          :key="item.url"
          @click="setImgCanvas(item.url)"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
import vue from "vue";
//import $ from "jquery";
import Swiper from "../../js/swiper-bundle.js";

export default {
  data() {
    return {
      banners: [
        { url: "../../images/avator.jpg" },
        { url: "../../images/4.jpg" },
        { url: "../../images/5.jpg" },
        { url: "../../images/6.jpg" },
        { url: "../../images/7.jpg" },
        { url: "../../images/8.jpg" },
        { url: "../../images/1.png" },
        { url: "../../images/2.png" },
        { url: "../../images/3.png" },
        { url: "../../images/4.png" },
        { url: "../../images/5.png" },
      ],
      imgElement: {},
      canvas: {},
      firstpoint: {},
      lastPoint: {},
      contextMenuItems: {},
      position:{},
    };
  },
  methods: {
    setImgCanvas(url) {
      //获取到
      //  this.setBackgroundImage(url);
      this.imgElement.src = url;
      this.canvas.clear();
      this.canvas.setBackgroundImage(this.imgInstance);
     
    },

    drawRect(t, l, w, h) {
      var rect = new fabric.Rect({
        top: l, //距离画布上边的距离
        left: t, //距离画布左侧的距离，单位是像素
        width: w, //矩形的宽度
        height: h, //矩形的高度
        fill: "#00000000",
        evented: false, //rect 无法被选中
        stroke: "red", // 边框原色
        strokeWidth: 3, // 边框大小
      });

      //canvas.add(new fabric.Group([imgInstance,rect]));
      this.canvas.add(rect);
    },
   onContextmenu(event) {
     console.log("右键");
      var pointer = this.canvas.getPointer(event.originalEvent);
      var objects = this.canvas.getObjects();
      for (var i = objects.length - 1; i >= 0; i--) {
        var object = objects[i];
        //判断该对象是否在鼠标点击处
        if (this.canvas.containsPoint(event, object)) {
          //选中该对象
          this.canvas.setActiveObject(object);
          //alert("你好，我是一个警告框！")
          //显示菜单
          this.showContextMenu(event, object);
          //alert("eee")
          continue;
        }
      }

      //阻止系统右键菜单
      event.preventDefault();
      return false;
    },
    showContextMenu(event, object) {
      //定义右键菜单项
      this.contextMenuItems = {
        delete: { name: "删除", icon: "delete", data: object },
        add: { name: "新增标签", icon: "add", data: object },
        label: { name: "标签", icon: "label", data: object },
      };
      //右键菜单显示位置
      this.position = {
        x: event.clientX,
        y: event.clientY,
      };
      //alert("dfnk")
      // 显示菜单
      $("#contextmenu-output").contextMenu(this.position);
    },
    contextMenuClick(key, options) {
      consolo.log("click 右键");
      if (key == "delete") {
        //得到对应的object并删除
        var object = this.contextMenuItems[key].data;
        this.canvas.remove(object);
      } else if ((key = "label")) {
        alert(this.contextMenuItems[key].name);
      }
    },
    
  },
  mounted() {
    //  设置图片缩略图
    var galleryThumbs = new Swiper(".gallery-thumbs", {
      spaceBetween: 10,
      slidesPerView: 4,
      freeMode: true,
      watchSlidesVisibility: true,
      watchSlidesProgress: true,
    });
    var galleryTop = new Swiper(".gallery-top", {
      spaceBetween: 10,
      navigation: {
        nextEl: ".swiper-button-next",
        prevEl: ".swiper-button-prev",
      },
      thumbs: {
        swiper: galleryThumbs,
      },
    });

    //  设置canvas
    let painting = false;
    this.firstpoint = { x: undefined, y: undefined };
    this.lastPoint = { x: undefined, y: undefined };
    this.imgElement = document.getElementById("img");
    this.canvas = new fabric.Canvas("canvas");
  
    // $('canvas').css("background-size","150px 123px");
    this.imgInstance = new fabric.Image(this.imgElement, {
      //设置图片在canvas中的位置和样子
      left: 0,
      top: 0,
      width: 400,
      height: 240,
      // scaleX: 1,
      // scaleY: 1
      //angle:30//设置旋转
    });


    //  window.onload = function () {
    //   //添加右击事件
    //   $(".upper-canvas").contextmenu(this.onContextmenu);
    //   //初始化右键菜单
    //   $.contextMenu({
    //     selector: "#contextmenu-output",
    //     trigger: "none",
    //     build: function ($trigger, e) {
    //       //构建菜单项build方法在每次右键点击会执行
    //       return {
    //         callback: this.contextMenuClick,
    //         items: this.contextMenuItems,
    //       };
        
    //     },
    //   });
      
    // }

    this.canvas.setBackgroundImage(this.imgInstance);
    this.canvas.selections = false; //取消框选

    this.canvas.on("mouse:down", (options) => {
      //var options = ev || window.event;
      var x = options.e.clientX;
      var y = options.e.clientY;
      this.firstPoint = { x: x, y: y };
      console.log("start", options.e.clientX, options.e.clientY);
      
    });

    this.canvas.on("mouse:move", function (options) {
      //var options = ev || window.event;
      //console.log("move", options.e.clientX, options.e.clientY)
    });
    this.canvas.on("mouse:up", (options) => {
      //var options = ev || window.event;
      var x = options.e.clientX;
      var y = options.e.clientY;
      this.lastPoint = { x: x, y: y };
      this.drawRect(
        this.firstPoint.x,
        this.firstPoint.y,
        this.lastPoint.x - this.firstPoint.x,
        this.lastPoint.y - this.firstPoint.y
      );
      console.log(
        "end",
        this.firstPoint.x,
        this.firstPoint.y,
        this.lastPoint.x,
        this.lastPoint.y
      );
      //console.log("up", options.e.clientX, options.e.clientY)
    });
    // $(".upper-canvas").contextmenu(this.addLabel);
    $(".upper-canvas").contextmenu(this.onContextmenu);
       $.contextMenu({
        selector: "#contextmenu-output",
        trigger: "none",
        build: function ($trigger, e) {
          //构建菜单项build方法在每次右键点击会执行
          return {
            callback: this.contextMenuClick,
            items: this.contextMenuItems,
          };
        
        },
      });
   
  },
};
</script>
<style lang="scss">
body {
  position: relative;
}
.container {
  // position: relative;
  height: 240px;
  width: 400px;
  margin: 0;
  padding: 0;
}

.canvas-container {
  position: absolute;
  left: 0;
  top: -189px;
  z-index: 9999 !important;
}

.upper-canvas {
  box-sizing: border-box;
  border: 1px solid #ccc;
  box-shadow: 5px 5px 3px #888888;
}

.swiper-container {
  width: 100%;
  height: 240px;
  margin-left: auto;
  margin-right: auto;
}

#sel {
  position: absolute;
  left: 100px;
  top: 100px;
  width: 60px;
  display: none;
  z-index: 999999 !important;
}

.swiper-container.first-wrapper {
  position: absolute;
  left: 0;
  top: 0;
  display: none !important;
  height: 0;
}
.swiper-slide {
  background-position: center;
}
.gallery-thumbs {
  display: absolute;
  left: 0;
  top: -185px;
}

.gallery-top {
  height: 80%;
  width: 100%;
}

.gallery-thumbs {
  height: 20%;
  box-sizing: border-box;
  padding: 10px 0;
  background-color: black;
}

.gallery-thumbs .swiper-slide {
  width: 25%;
  height: 100%;
  opacity: 0.4;
  background-size: 100% 100%;
}

.gallery-thumbs .swiper-slide-thumb-active {
  opacity: 1;
}
</style>