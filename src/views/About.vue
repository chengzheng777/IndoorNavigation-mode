<template>
  <div class="container">
    <div id="content"></div>
    <div class="click-list-left">
      <div class="list-floor">1</div>
      <div class="list-zoom flex-column">
        <div class="list-zoom-el zoom-big" @click="zoomBig">+</div>
        <div class="list-zoom-el zoom-small" @click="zoomSmall">-</div>
      </div>
      <div class="list-rotate">3</div>
    </div>
  </div>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { MTLLoader } from 'three/examples/jsm/loaders/MTLLoader.js';
// import { OBJLoader2 } from 'three/examples/jsm/loaders/OBJLoader2.js';       // 此加载器不知道为啥mtl材质出不来。
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader'
import { MtlObjBridge } from 'three/examples/jsm/loaders/obj2/bridge/MtlObjBridge.js'

export default {
  name: 'about',
  data() {
    return {
      // renderer: '', // 渲染器
      // camera: '', // 相机
      // scene: '',  // 场景
      // light: '',  // 光线
      // mesh: '',   // 网格
      // controls: '', // 控制
      // baseUrl: process.env.BASE_URL
      publicPath: process.env.BASE_URL,
      // 相机向右，展现出 模型向左旋转
      rLeft: 30,
    }
  },
  components: {

  },
  mounted() {
    // this.$nextTick(function () {
    this.init()
    // })
    // this.init();
    //render(); // remove when using next line for  loop (requestAnimationFrame)
  },
  methods: {
    init() {
      // 锁定 this
      let _this = this;
      //场景
      let scene = new THREE.Scene();

      //相机
      let camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
      //设置相机坐标
      console.log("设置相机坐标之前：：：", _this.rLeft)
      camera.position.set(_this.rLeft, 80, 100);
      console.log("设置相机坐标：：：", _this.rLeft)
      //渲染器
      let renderer = new THREE.WebGLRenderer();
      //设置渲染器的颜色和大小
      renderer.setClearColor(0x404040);
      renderer.setSize(window.innerWidth, window.innerHeight);
      //将renderer（渲染器）的dom元素（renderer.domElement）添加到我们的HTML文档中。
      let container = document.getElementById('content');
      //这就是渲染器用来显示场景给我们看的<canvas>元素
      container.appendChild(renderer.domElement);

      //鼠标控制旋转
      let controls = new OrbitControls(camera, renderer.domElement);
      //设置是否可以缩放,默认值为true
      // controls.enableZoom = false;
      //设置鼠标交互,设置为false之后，不能移动位置，不能旋转物体,默认为true
      // controls.enableRotate = false;

      //设置光源
      let light = new THREE.DirectionalLight(0xffffff, 0.5);
      light.position.setScalar(100);
      // scene.add(AmbientLightlight);
      scene.add(new THREE.AmbientLight(0xffffff, 0.5));

      //添加灰色网格线
      scene.add(new THREE.GridHelper(40, 40));
      //导入材质
      let mtlLoader = new MTLLoader();
      let objLoader = new OBJLoader();
      mtlLoader.load(`${_this.publicPath}obj/cs.mtl`,
        // 资源加载后成功执行的函数
        function (materials) {
          console.log("材质加载后:::", materials)
          materials.preload();
          //导入obj模型
          objLoader.setMaterials(materials, true);
          // objLoader.setMaterials(materials);
          objLoader.load(`${_this.publicPath}obj/cs.obj`,
            // 模型已经加载
            function (object) {
              //设置模型缩放比例
              object.scale.set(0.1, 0.1, 0.1);
              //设置模型的坐标
              object.position.set(0, 0, 0);
              //将模型添加到场景中
              scene.add(object);
            },
            // 正在加载时候
            function (xhr) {
              console.log("正在加载时进度：：：", (xhr.loaded / xhr.total * 100) + '%loaded');
            },
            // 加载错误的时候
            function (error) {
              console.log("加载错误:::", error)
            }
          );
        }
      )

      mtlLoader.load(`${_this.publicPath}obj/cs.mtl`,
        // 资源加载后成功执行的函数
        function (materials) {
          console.log("材质加载后:::", materials)
          materials.preload();
          //导入obj模型
          objLoader.setMaterials(materials, true);
          // objLoader.setMaterials(materials);
          objLoader.load(`${_this.publicPath}obj/cs.obj`,
            // 模型已经加载
            function (object) {
              //设置模型缩放比例
              object.scale.set(0.1, 0.1, 0.1);
              //设置模型的坐标
              object.position.set(0, -30, 0);
              //将模型添加到场景中
              scene.add(object);
            },
            // 正在加载时候
            function (xhr) {
              console.log("正在加载时进度：：：", (xhr.loaded / xhr.total * 100) + '%loaded');
            },
            // 加载错误的时候
            function (error) {
              console.log("加载错误:::", error)
            }
          );
        }
      )

      animate();
      function animate() {
        controls.update();
        //动画循环渲染
        requestAnimationFrame(animate);
        //渲染到页面上
        renderer.render(scene, camera);
        // this.labelRenderer.render(this.scene, this.camera);
      }
    },
    // 放大
    zoomBig() {
      let _this = this;
      console.log("放大视角")
      _this.rLeft += 30;
      _this.init();
      console.log("放大：：：", _this.rLeft)
    },
    // 缩小
    zoomSmall() {
      console.log("缩小视角")
    }
  }
}
</script>

<style lang="scss" scoped >
.container {
  width: 100vw;
  height: 100vh;
  position: relative;
}
#content {
  width: 100vw;
  height: 100vh;
  position: absolute;
}
.click-list-left {
  z-index: 100;
  position: absolute;
}

.list-floor,
.list-zoom,
.list-rotate {
  // z-index: 1000;
  width: 10vw;
  height: 10vh;
  background-color: aliceblue;
  margin: 2vw;
  border-radius: 5vw;
}

.list-zoom {
  // border-radius: 5vw;
}
.list-zoom-el {
  flex: 1;
  border: 1px solid #333;
}

.flex-row {
  display: flex;
  flex-flow: row nowrap;
  justify-content: center;
  align-items: center;
}
.flex-column {
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: center;
}
</style>