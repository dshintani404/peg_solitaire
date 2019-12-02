<template>
    <canvas id='canvas'/>
</template>

<script>
/* eslint-disable no-console */
import * as THREE from 'three';

export default {
    name: 'Pegsolitaire',
    data() {
        const renderer = null
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(30, window.visualViewport.width / window.visualViewport.height, 1, 1000);
        camera.position.set(0, 0, 20);

        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        const g_sphere = new THREE.SphereGeometry(0.35, 32, 32);
        const canvas = null
        const canvas_shape = null
        const dist = 0.8
        const positions = [
            [0, 0],
            [dist, 0],
            [-dist, 0],
            [0, dist],
            [dist, dist],
            [-dist, dist],
            [0, -dist],
            [dist, -dist],
            [-dist, -dist],
        ];
        const centors = [
            [0, 0],
            [dist * 3, 0],
            [-dist * 3, 0],
            [0, dist * 3],
            [0, -dist * 3],
        ]
        const spheres = [];

        for(var i=0; i<45; i++) {
            const m_sphere = new THREE.MeshPhysicalMaterial({
                color: "rgb(0, 255, 0)",
                metalness: 0.99,
                roughness: 0,
                });
            const sphere = new THREE.Mesh(g_sphere, m_sphere);
            sphere.position.x = positions[i%9][0] + centors[Math.floor(i/9)][0];
            sphere.position.y = positions[i%9][1] + centors[Math.floor(i/9)][1];
            scene.add(sphere)
            spheres.push(sphere)
        }
        spheres[0].material.color = new THREE.Color("rgb(255,0,0)")
        const first_sphere = null

        const light = new THREE.SpotLight(0xffffff);
        light.power = 280
        light.position.set(20,20,30)
        scene.add(light)

        const light2 = new THREE.SpotLight(0xffffff);
        light2.power = 120
        light2.position.set(-10,0,30)
        scene.add(light2)
        scene.background = new THREE.Color("rgb(240, 240, 236)")
        
        return {
            scene, camera, spheres, renderer, raycaster, mouse, canvas, canvas_shape, first_sphere, dist
        };
    },
    mounted() {
        const canvas = document.getElementById("canvas");
        canvas.width = window.visualViewport.width
        canvas.height = window.visualViewport.height
        this.canvas = canvas; 
        const renderer = new THREE.WebGLRenderer({
            antialias: true,
            canvas: canvas
        });
        renderer.setPixelRatio(window.devicePixelRatio)
        renderer.setSize(window.visualViewport.width, window.visualViewport.height)
        this.renderer = renderer
        this.canvas_shape = canvas.getBoundingClientRect()
        renderer.render(this.scene, this.camera);
        document.addEventListener('mousedown', this.onDocumentMouseDown, false);

        window.addEventListener('resize', this.onWindowResize, false);
    },
    methods: {
        onWindowResize() {
            this.canvas.width = window.visualViewport.width
            this.canvas.height = window.visualViewport.height
            this.camera.aspect = window.visualViewport.width / window.visualViewport.height;
            this.camera.updateProjectionMatrix();
            this.renderer.setSize(window.visualViewport.width, window.visualViewport.height);
            this.canvas_shape = this.canvas.getBoundingClientRect()
        },        
        onDocumentMouseDown(e) {
            e.preventDefault();
            const x = 2 * (e.clientX - this.canvas_shape.left) / this.canvas_shape.width - 1
            const y = 1 - 2 * (e.clientY - this.canvas_shape.top) / this.canvas_shape.height

            this.mouse.set(x, y);
            this.raycaster.setFromCamera(this.mouse, this.camera);
            const intersects = this.raycaster.intersectObjects(this.spheres);
            if (intersects.length > 0) {
                const intersect = intersects[0];
                if (this.first_sphere == null || this.first_sphere == undefined) {
                    if (intersect.object.material.color.g == 1) {
                        intersect.object.material.color = new THREE.Color("rgb(255,215,0)")
                        this.first_sphere = intersect.object
                    }
                } else {
                    if (intersect.object.material.color.r == 1 & Math.round(Math.sqrt((intersect.object.position.x - this.first_sphere.position.x)**2 + (intersect.object.position.y - this.first_sphere.position.y)**2) * 10) == 20 * this.dist) {
                        this.first_sphere.material.color = new THREE.Color("rgb(255,0,0)")

                        const mid_x = (intersect.object.position.x + this.first_sphere.position.x) * 0.5;
                        const mid_y = (intersect.object.position.y + this.first_sphere.position.y) * 0.5;
                        this.mouse.set(mid_x*0.14, mid_y*0.20);
                        this.raycaster.setFromCamera(this.mouse, this.camera);
                        const mid_intersects = this.raycaster.intersectObjects(this.spheres);
                        if(mid_intersects[0].object.material.color.g == 1) {
                            intersect.object.material.color = new THREE.Color("rgb(0,255,0)")
                            mid_intersects[0].object.material.color = new THREE.Color("rgb(255,0,0)")
                        }
                    } else {
                        this.first_sphere.material.color = new THREE.Color("rgb(0,255,0)")
                    }
                    this.first_sphere = null
                }
            }
            this.render()
        },
        render() {
            this.renderer.render(this.scene, this.camera);
        },
    },
    destroyed() {
        document.removeEventListener('mousedown', this.onDocumentMouseDown, false);
    }
};
</script>        
