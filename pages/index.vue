<template>
    <div>
      <Hero />
    </div>
</template>

<script type="module">
import * as THREE from 'three';

export default{
  data() {
    return {
      renderer: null,
      scene: null,
      camera: null,
      mouseX : 0,
      mouseY : 0,

    }
  },
  methods: {
    init: function() {
      this.camera = new THREE.PerspectiveCamera(55, window.innerWidth / window.innerHeight, 2, 2000);
      this.camera.position.z = 1000;

      this.scene = new THREE.Scene();
      this.scene.fog = new THREE.FogExp2(0x000000, 0.001);

      const geometry = new THREE.BufferGeometry();
      const vertices = [];

      const sprite = new THREE.TextureLoader().load( 'disc.png');

      for (let i = 0; i < 10000; i++) {

        const x = 2000 * Math.random() - 1000;
        const y = 2000 * Math.random() - 1000;
        const z = 2000 * Math.random() - 1000;

        vertices.push(x, y, z);
      }

      geometry.setAttribute('position', new THREE.Float32BufferAttribute(vertices, 3));

      this.material = new THREE.PointsMaterial({ size: 35, sizeAttenuation: true, map: sprite, alphaTest: 0.5, transparent: true });
      this.material.color.setHSL(1.0, 0.3, 0.7);

      const particles = new THREE.Points(geometry, this.material);
      this.scene.add(particles);

      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(this.renderer.domElement);

      // stats = new Stats();
      // document.body.appendChild(stats.dom);
      // const gui = new GUI();
      // gui.add(material, 'sizeAttenuation').onChange(function () {
      //   material.needsUpdate = true;
      // });
      // gui.open();
      document.body.style.touchAction = 'none';
      document.body.addEventListener('pointermove', this.onPointerMove);
      window.addEventListener('resize', this.onWindowResize);
    },

    animate: function() {
      requestAnimationFrame(this.animate);

      this.render();
    },

    onWindowResize: function() {

      this.windowHalfX = window.innerWidth / 2;
      this.windowHalfY = window.innerHeight / 2;

      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();

      this.renderer.setSize(window.innerWidth, window.innerHeight);

    },

    onPointerMove: function(event) {

      if (event.isPrimary === false) return;

      this.mouseX = event.clientX - this.windowHalfX;
      this.mouseY = event.clientY - this.windowHalfY;

    },
    render: function () {

      const time = Date.now() * 0.00005;

      this.camera.position.x += (this.mouseX - this.camera.position.x) * 0.05;
      this.camera.position.y += (- this.mouseY - this.camera.position.y) * 0.05;

      this.camera.lookAt(this.scene.position);

      const h = (360 * (1.0 + time) % 360) / 360;
      this.material.color.setHSL(h, 0.5, 0.5);

      this.renderer.render(this.scene, this.camera);

    }
  },
  mounted() {
    this.container = this.$refs.container;
    this.windowHalfX = window.innerWidth / 2;
		this.windowHalfY = window.innerHeight / 2;
    this.init()
    this.animate()
  }
}
</script>
