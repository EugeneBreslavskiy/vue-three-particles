<template>
    <section class="scene" ref="scene"></section>
</template>

<script>
    import * as THREE from 'three'
  
    export default {
        name: 'Scene',
        data() {
            return {
                container: null,
                camera: null,
                scene: null,
                renderer: null,
                stars: null
            }
        },
        methods: {
            init() {
                this.container = this.$refs.scene
                
                this.scene = new THREE.Scene()
                this.scene.background = new THREE.Color( 0x000000 )
                
                this.createCamera()
                this.createLights()
                this.createRenderer()
                this.createParticles()
                
                this.renderer.setAnimationLoop(() => {
                    this.update()
                    this.render()
                })

                window.addEventListener( 'resize', this.resizeWindowHandler, false )
            },
            createCamera() {
                this.camera = new THREE.PerspectiveCamera( 45, window.innerWidth / window.innerHeight, 1, 2000 )
                this.camera.position.set( 0, 0, 20 )
            },
            createLights() {
                const ambientLight = new THREE.AmbientLight( 0xf2f2f2, 2 )
                
                this.scene.add( ambientLight )
            },
            createRenderer() {
                this.renderer = new THREE.WebGLRenderer( { antialias: true } )
                this.renderer.setSize( window.innerWidth, window.innerHeight )

                this.renderer.setPixelRatio( window.devicePixelRatio )

                this.container.appendChild( this.renderer.domElement )
            },
            createParticles() {
                let count = 8000
                let sprite = new THREE.TextureLoader().load('/static/images/nova.png')
                let geometry = new THREE.Geometry()
                let material = new THREE.PointsMaterial({
                    size: 0.85,
                    map: sprite
                })
                
                while ( count ) {
                    let star = new THREE.Vector3(
                        Math.random() * 400 - 200,
                        Math.random() * 400 - 200,
                        Math.random() * 400 - 200
                    )
                    
                    star.velocity = 0.99
                    
                    geometry.vertices.push(star)
                    
                    count--
                }
                
                this.stars = new THREE.Points( geometry, material )
                
                this.scene.add( this.stars )
            },
            update() {
                this.stars.geometry.vertices.forEach((star) => {
                    star.z += star.velocity
                    
                    if ( star.z >= 200 ) {
                        star.z = -200
                    }
                })
                
                this.stars.geometry.verticesNeedUpdate = true
            },
            render() {
                this.renderer.render( this.scene, this.camera )
            },
            resizeWindowHandler() {
                this.camera.aspect = window.innerWidth / window.innerHeight
                this.camera.updateProjectionMatrix()

                this.renderer.setSize( window.innerWidth, window.innerHeight )
            }
        },
        mounted() {
            this.init()
            console.log(this.stars)
        }
    }
</script>

<style lang="sass">
  
  .scene
    width: 100%
    height: 100%
    position: absolute

</style>
