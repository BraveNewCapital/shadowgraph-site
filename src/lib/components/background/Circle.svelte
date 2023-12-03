<script>
    import * as THREE from "three";
    import { onMount } from "svelte";

    import { EffectComposer } from "three/examples/jsm/postprocessing/EffectComposer.js";
    import { RenderPass } from "three/examples/jsm/postprocessing/RenderPass.js";
    import { ShaderPass } from "three/examples/jsm/postprocessing/ShaderPass.js";
    import { FXAAShader } from "three/examples/jsm/shaders/FXAAShader.js";

    let canvas;

    onMount(() => {
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5;

        const renderer = new THREE.WebGLRenderer({ canvas: canvas, antialias: true });
        const container = canvas.parentElement;
        renderer.setSize(container.clientWidth, container.clientHeight);

        const composer = new EffectComposer(renderer);
        const renderPass = new RenderPass(scene, camera);
        composer.addPass(renderPass);

        const fxaaPass = new ShaderPass(FXAAShader);
        const pixelRatio = renderer.getPixelRatio();
        fxaaPass.material.uniforms['resolution'].value.x = 1 / (window.innerWidth * pixelRatio);
        fxaaPass.material.uniforms['resolution'].value.y = 1 / (window.innerHeight * pixelRatio);
        composer.addPass(fxaaPass);

        const geometry = new THREE.PlaneGeometry(2, 2);
        const uniforms = {
            iResolution: { value: new THREE.Vector3(window.innerWidth, window.innerHeight, 1) },
            iTime: { value: 0.0 },
            iTimeDelta: { value: 0.0 },
            iFrameRate: { value: 60.0 },
            iFrame: { value: 0.0 },
            iChannelTime: { value: [0.0, 0.0, 0.0, 0.0] },
            iChannelResolution: {
                value: [
                    new THREE.Vector3(512, 1, 1),
                    new THREE.Vector3(512, 1, 1),
                    new THREE.Vector3(512, 1, 1),
                    new THREE.Vector3(512, 1, 1)
                ]
            },
            iMouse: { value: new THREE.Vector4() },
            iDate: { value: new THREE.Vector4() },
            iSampleRate: { value: 54100.0 },
            iChannel0: { value: 0 }
        };

        const fragmentShaderCode = `
        // Uniforms and Constants
        uniform vec3 iResolution; // Resolution of the screen
        uniform float iTime; // Current time
        uniform float iTimeDelta; // Time since last frame
        uniform float iFrameRate; // Frame rate
        uniform int iFrame; // Current frame number
        uniform float iChannelTime[4]; // Channel time for 4 channels
        uniform vec3 iChannelResolution[4]; // Resolution for 4 channels
        uniform vec4 iMouse; // Mouse coordinates and button states
        uniform vec4 iDate; // Current date
        uniform float iSampleRate; // Audio sample rate

        #define PI 3.14159265359

        // Global Variables and Constants
        const float AnimTimeMultiplier = 2.618;
        const vec4 FFTNormalizationFactors = vec4(12, 36, 144, 320);
        const float PhaseOffsetMultiplier = PI / 3.5;
        const float RingCount = 12.0;
        const float AngleModifier = PI / 8.0;
        const float GammaCorrectionValue = 0.4445;
        const vec3 EyeCenterIntensity = vec3(0.1, 0.9, 0.0015);
        const float NegativeCorrectionFactor = -0.005;
        const float RingIntensityFactor = 0.9;
        const float RingWidthFactor = 0.8;
        const float ColorAmplitudeMultiplier = 0.1;
        const float ColorFrequencyOffset = PI / 3.0;
        const float DistanceThreshold = 0.0025;
        const float CenterEyeIntensityMultiplier = 0.002;
        const float MouseFrequencyMultiplier = 40.0; // Added for more noticeable mouse effect

        float animTime; // Animated time
        vec4 fft; // Fourier transform components

        // Function Definitions

        float sinRad2(float angle, float radiusSquared) {
            return radiusSquared * (1.0 + 0.25 * fft.x * sin(8.0 * angle + animTime));
        }

        float sinRing(float distanceSquared, float angle, float radiusSquared, float widthFactor) {
            float r2Min = sinRad2(angle, radiusSquared);
            float r2Max = sinRad2(angle, radiusSquared * widthFactor);
            if ((distanceSquared >= r2Min - DistanceThreshold) && (distanceSquared <= r2Max + DistanceThreshold)) return 1.0;
            return CenterEyeIntensityMultiplier / abs(distanceSquared - r2Min) + CenterEyeIntensityMultiplier / abs(distanceSquared - r2Max);
        }

        float sinRing2(float distanceSquared, float angle, float radiusSquared, float widthFactor) {
            return sinRing(distanceSquared, angle, radiusSquared, widthFactor) + sinRing(distanceSquared, angle + AngleModifier, radiusSquared, widthFactor);
        }

        // Main Image Rendering Function
        void mainImage(out vec4 fragColor, in vec2 fragCoord) {
            vec3 col = vec3(0);
            vec2 uv = (2.0 * fragCoord - iResolution.xy) / max(iResolution.x, iResolution.y);
            animTime = AnimTimeMultiplier * iTime;
            float a = atan(uv.x, uv.y); // Polar angle (limited)
            float d2 = uv.x * uv.x + uv.y * uv.y; // Polar distance squared
            float p, s, amp, pc;

            // Mouse position affects frequency
            float mouseXFactor = iMouse.x / iResolution.x * MouseFrequencyMultiplier;
            float mouseYFactor = iMouse.y / iResolution.y * MouseFrequencyMultiplier;

            // Simulated FFT Value Generation (Simulating Music)
            float timeFactor = iTime * 10.0;
            fft.x = abs(sin((timeFactor + mouseXFactor) * 0.1 + sin((timeFactor + mouseYFactor) * 0.014))); // Bass frequency
            fft.y = abs(sin((timeFactor + mouseXFactor) * 0.2 + sin((timeFactor + mouseYFactor) * 0.031))); // Mid frequency
            fft.z = abs(sin((timeFactor + mouseXFactor) * 0.4 + sin((timeFactor + mouseYFactor) * 0.042))); // High frequency
            fft.w = abs(sin((timeFactor + mouseXFactor) * 0.8 + sin((timeFactor + mouseYFactor) * 0.053))); // Additional harmonic
            fft /= FFTNormalizationFactors; // Normalize

            // Geometry
            for (float n = 0.0; n < RingCount; n++) {
                p = 0.01 * animTime + n * PhaseOffsetMultiplier; // Phase
                s = smoothstep(NegativeCorrectionFactor, 0.0, -cos(p));
                pc = a + p + animTime; // Phase color
                amp = (RingIntensityFactor - RingWidthFactor * s) * sinRing2(d2, (-1.0 + 2.0 * mod(n, 2.0)) * a, 0.9 * (1.0 + 0.999 * sin(p)), 1.0 + 0.5 * s); // Rings
                col += amp * (ColorAmplitudeMultiplier + 0.9 * fft.zyw) * vec3(sin(pc), sin(pc + ColorFrequencyOffset), sin(pc + 2.0 * ColorFrequencyOffset)); // Colors
            }

            // Finalizations
            col *= col; // Negative correction & harder falloff
            col += vec3(CenterEyeIntensityMultiplier / abs(d2 - EyeCenterIntensity.z) * (EyeCenterIntensity.x + EyeCenterIntensity.y * fft.x)); // Center eye
            col = pow(col, vec3(GammaCorrectionValue)); // Gamma correction
            fragColor = vec4(col, 1.0);
        }

        // Entry point
        void main() {
            mainImage(gl_FragColor, gl_FragCoord.xy);
        }

        `;

        const material = new THREE.ShaderMaterial({
            uniforms: uniforms,
            fragmentShader: fragmentShaderCode,
            vertexShader: `
                void main() {
                    gl_Position = vec4(position, 1.0);
                }
            `
        });

        const mesh = new THREE.Mesh(geometry, material);
        scene.add(mesh);

        function animate() {
            requestAnimationFrame(animate);
            material.uniforms.iTime.value += 0.005;
            material.uniforms.iFrame.value += 1;
            composer.render(scene, camera);
        }

        window.addEventListener('resize', () => {
            const container = canvas.parentElement;
            const width = container.clientWidth;
            const height = container.clientHeight;

            renderer.setSize(width, height);
            camera.aspect = width / height;
            camera.updateProjectionMatrix();

            material.uniforms.iResolution.value.set(width, height, 1);

            // Update FXAA pass resolution uniforms
            const pixelRatio = renderer.getPixelRatio();
            fxaaPass.material.uniforms['resolution'].value.x = 1 / (width * pixelRatio);
            fxaaPass.material.uniforms['resolution'].value.y = 1 / (height * pixelRatio);

            composer.setSize(width, height);
        });

        animate();
    });
</script>

<canvas bind:this={canvas} class="w-full"></canvas>
