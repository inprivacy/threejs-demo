<script setup lang="ts">
import * as THREE from 'three'
import { ref, onMounted } from "vue"
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader"
let screenDom = ref(null)

onMounted(() => {
  //创建场景
  let scene = new THREE.Scene()
  //创建相机
  let camera = new THREE.PerspectiveCamera(
    100,
    screenDom.value.clientWidth / screenDom.value.clientHeight,
    0.1,
    1000
  );
  camera.position.set(0, 1, 5);

  //创建渲染器
  let renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(screenDom.value.clientWidth, screenDom.value.clientHeight);
  screenDom.value.appendChild(renderer.domElement)

  //添加控制器
  let control = new OrbitControls(camera, renderer.domElement)

  // 创建天空盒
  // 相应面对应相应图片
  const imgUrl = [
    'src/assets/瓷砖.jpg',
    'src/assets/瓷砖.jpg',
    'src/assets/瓷砖.jpg',
    'src/assets/瓷砖.jpg',
    'src/assets/瓷砖.jpg',
    'src/assets/瓷砖.jpg',
  ]
  // 调用getTexturesFromAtlasFile() 给每个材质加上相应的图片
  const textures = getTexturesFromAtlasFile(imgUrl, 6)
  const materials = []

  for (let i = 0; i < 6; i++) {
    // 创造六个面的材质
    materials.push(new THREE.MeshBasicMaterial({ map: textures[i] }))
  }

  //创造包围盒
  const skyBox = new THREE.Mesh(new THREE.BufferGeometry(1024, 1024, 1024), materials)
  // skyBox.position.set(0, 0, 0);
  skyBox.geometry.scale(1, 1, -1)
  scene.add(skyBox)
  // 六个面添加图片
  function getTexturesFromAtlasFile(atlasImgUrl:any, tilesNum:any) {
    const textures:any = []

    for (let i = 0; i < tilesNum; i++) {
      textures[i] = new THREE.Texture()
    }

    for (let i = 0; i < textures.length; i++) {
      const imageObj = new Image()
      imageObj.src = atlasImgUrl[i]
      imageObj.onload = () => {
        let context = ''
        // let tileWidth = imageObj.height;
        // let tileWidth = 5000;

        const canvas = document.createElement('canvas')
        // const canvas: HTMLCanvasElement = this.canvasRef.nativeElement;  // 得到canvas 元素
        context = canvas.getContext('2d')
        const canvasHeight = 720
        canvas.height = canvasHeight
        canvas.width = canvasHeight
        // context.drawImage( imageObj, canvasHeight * i, 0, canvasHeight, canvasHeight, 0, 0, canvasHeight, canvasHeight );
        context.drawImage(imageObj, 0, 0, canvasHeight, canvasHeight)
        textures[i].image = canvas
        textures[i].needsUpdate = true
      }
    }
    return textures
  }

  //添加模型
  const loader = new GLTFLoader()
  loader.load("src/assets/officeBuilding.gltf", (gltf) => {
    gltf.scene.position.set(0, 0, 0)   // 模型位置
    gltf.scene.rotation.x = 0     // x轴旋转
    gltf.scene.rotation.y = 0     // y轴旋转
    gltf.scene.rotation.z = 0   // z轴旋转
    scene.add(gltf.scene)   // 加入场景
  })
  // loader.load("src/assets/pig.gltf", (gltf) => {
  //   gltf.scene.scale.set(1, 1, 1);
  //   gltf.scene.position.set(0, -4, 0)
  //   scene.add(gltf.scene)
  // })
  loader.load("src/assets/rabbit.gltf", (gltf) => {
    gltf.scene.scale.set(20, 20, 20);
    gltf.scene.position.set(30, 0, 0)
    gltf.scene.rotation.x = 0
    gltf.scene.rotation.y = -1
    gltf.scene.rotation.z = 0
    scene.add(gltf.scene)
  })

  //添加直线光
  let light1 = new THREE.DirectionalLight(0xffffff, 1)
  light1.position.set(0, 50, 50)
  let light2 = new THREE.DirectionalLight(0xffffff, 1)
  light2.position.set(0, 50, -50)
  let light3 = new THREE.DirectionalLight(0xffffff, 1)
  light3.position.set(50, 50, 50)
  let light4 = new THREE.DirectionalLight(0xffffff, 1)
  light4.position.set(-50, -10, 0)
  let light5 = new THREE.DirectionalLight(0xffffff, 1)
  light5.position.set(0, 0, 50)
  let light6 = new THREE.DirectionalLight(0xffffff, 1)
  light6.position.set(0, 0, -50)
  let light7 = new THREE.DirectionalLight(0xffffff, 1)
  light7.position.set(50, 0, 0)
  let light8 = new THREE.DirectionalLight(0xffffff, 1)
  light8.position.set(-50, 0, 0)
  scene.add(light1, light2, light3, light4, light5, light6, light7, light8)

  //绘制画布
  function render() {
    requestAnimationFrame(render);
    renderer.render(scene, camera)
  }
  render()
})

</script>

<template>
  <div class="canvas-container" ref="screenDom"></div>
</template>

<style scoped>
.canvas-container {
  width: 100vw;
  height: 100vh;
}
</style>
