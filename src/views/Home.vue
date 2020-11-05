<template>
  <div class="container">
    <div id="content"></div>
    <div class="click-list-left">
      <div class="list-floor">1</div>
      <div class="list-zoom flex-column">
        <div class="list-zoom-el zoom-big">+</div>
        <div class="list-zoom-el zoom-small">-</div>
      </div>
      <div class="list-rotate">3</div>
    </div>
  </div>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { MTLLoader } from 'three/examples/jsm/loaders/MTLLoader.js';
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader'
import { MtlObjBridge } from 'three/examples/jsm/loaders/obj2/bridge/MtlObjBridge.js';

export default {
  name: "Home",
  components: {
  },
  data() {
    return {
      // 场景
      scene: '',
      widthBox: '',
      heightBox: '',
      renderer: '',
      camera: '',
      light: '',

      publicPath: process.env.BASE_URL,
      controls: '',
    }
  },
  mounted() {
    this.threeStart()
  },
  methods: {
    /**
     * 初始化渲染器
     */
    initThree() {
      // 锁定this
      const _this = this;
      // 获取指定容器 宽，高
      _this.widthBox = document.getElementById('content').clientWidth;
      _this.heightBox = document.getElementById('content').clientHeight;
      // 渲染器
      _this.renderer = new THREE.WebGLRenderer();
      _this.renderer.setSize(_this.widthBox, _this.heightBox);
      // 将 renderer 的dom元素（renderer.domElement）添加到HTML文件中
      document.getElementById('content').appendChild(_this.renderer.domElement)
      this.renderer.setClearAlpha(0.2);
    },
    /**
     * 初始化场景
     */
    initScene() {
      const _this = this
      _this.scene = new THREE.Scene();
      // add 网格
      //添加灰色网格线
      _this.scene.add(new THREE.GridHelper(40, 40));
    },
    /**
     * 初始化相机
     */
    initCamera() {
      const _this = this;
      _this.camera = new THREE.PerspectiveCamera(45, _this.widthBox / _this.heightBox, 0.1, 2000);
      _this.camera.position.set(150, 250, 200);
    },
    /**
     * 初始化灯光
     */
    initLight() {
      const _this = this;
      _this.light = new THREE.AmbientLight(0xffffff);
      // 添加到场景中
      _this.scene.add(_this.light);
      let pointLight = new THREE.PointLight(0xe3e3e4);
      pointLight.position.set(300, 150, 350);
      _this.scene.add(pointLight);
    },
    /**
     * 初始化模型
     */
    initObject() {
      const _this = this;
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
              _this.scene.add(object);
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
    },

    /**
     * 用户交换事件
     */
    initControls() {
      const _this = this;
      _this.controls = new OrbitControls(_this.camera, _this.renderer.domElement)
      // 拉近摄像头
      _this.controls.minDistance = 5
      _this.controls.maxDistance = 600
      // 垂直旋转
      _this.controls.maxPolarAngle = Math.PI / 2
      _this.controls.minPolarAngle = Math.PI / 4

      _this.controls.target.set(0, 5, 0)
    },

    /**
     * 控制动画
     */
    animation() {
      const _this = this;
      _this.controls.update();
      requestAnimationFrame(_this.animation);
      _this.renderer.render(_this.scene, _this.camera);
    },

    /**
     * 集合
     */
    threeStart() {
      this.initThree()
      this.initCamera()
      this.initScene()
      this.initLight()
      this.initObject()
      this.initControls()
      this.animation()
    }
  }
};
</script>
