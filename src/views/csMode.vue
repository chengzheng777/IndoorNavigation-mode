<template>
    <div id="container">

    </div>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { MTLLoader } from 'three/examples/jsm/loaders/MTLLoader.js';
// import { OBJLoader2 } from 'three/examples/jsm/loaders/OBJLoader2.js';       // 此加载器不知道为啥mtl材质出不来。
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader';
import { MtlObjBridge } from 'three/examples/jsm/loaders/obj2/bridge/MtlObjBridge.js';

export default {
    name: 'ceshi',
    data() {
        return {

        }
    },
    mounted() {

    },
    methods: {
        
    }
}
</script>

<style>

</style>