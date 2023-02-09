<template>
    <div>
        <canvas ref="experience" />
    </div>
</template>

<script setup lang="ts">
import { Scene, PerspectiveCamera, Mesh, SphereGeometry, MeshBasicMaterial, WebGLRenderer, Color, Fog } from 'three'
import { Ref } from 'vue'
import { useWindowSize } from '@vueuse/core'

// Types
let renderer: WebGLRenderer
const experience: Ref<HTMLCanvasElement | null> = ref(null)

const { width, height } = useWindowSize()
const aspectRatio = computed(() => width.value / height.value)

// Create Scene
const bgColor = new Color('#E1F0C2')
const scene = new Scene()
scene.fog = new Fog(bgColor, 0.1, 75)
scene.background = bgColor

const camera = new PerspectiveCamera(75, aspectRatio.value, 0.1, 1000)
camera.position.set(0, 0, 4)
scene.add(camera)


// Create Sphere
const sphere = new Mesh(new SphereGeometry(1, 32, 32), new MeshBasicMaterial({ color: 0x008080 }))
scene.add(sphere)

// Update Camera
function updateCamera() {
    camera.aspect = aspectRatio.value
    camera.updateProjectionMatrix()
}

// Renderer
function updateRenderer() {
    renderer.setSize(width.value, height.value)
    renderer.render(scene, camera)
}

function setRenderer() {
    if (experience.value) {
        renderer = new WebGLRenderer({ canvas: experience.value, alpha: true, })
        updateRenderer()
    }
}

watch(aspectRatio, () => {
    updateRenderer()
})

onMounted(() => {
    setRenderer()
    loop()
})

const loop = () => {
    sphere.position.x += 0.01
    renderer.render(scene, camera)
    requestAnimationFrame(loop)
}
</script>

<style>
</style>
