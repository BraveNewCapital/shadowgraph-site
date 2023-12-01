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
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5;

        const renderer = new THREE.WebGLRenderer({ canvas: canvas, antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);

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
            iSampleRate: { value: 44100.0 },
            iChannel0: { value: 0 }
        };

        const fragmentShaderCode = `
            uniform vec3      iResolution;
            uniform float     iTime;
            uniform float     iTimeDelta;
            uniform float     iFrameRate;
            uniform int       iFrame;
            uniform float     iChannelTime[4];
            uniform vec3      iChannelResolution[4];
            uniform vec4      iMouse;
            uniform vec4      iDate;
            uniform float     iSampleRate;
            
            #define PI 3.14159265359 

            float animTime;
            vec4 fft;

            float sinRad2(float a, float r2){ //angle, radius squared
                return r2*(1.+.25*fft.x*sin(8.*a+animTime));
            }

            float sinRing(float d2, float a, float r2, float w){ //distance squared, angle, radius squared, width factor
                float r2Min = sinRad2(a,r2);
                float r2Max = sinRad2(a,r2*w);
                if ((d2>=r2Min-.001)&&(d2<=r2Max+.001)) return 1.;
                return .001/abs(d2-r2Min)+0.001/abs(d2-r2Max);
            }

            float sinRing2(float d2, float a, float r2, float w){ //distance squared, angle, radius squared, width factor
                return sinRing(d2,a,r2,w)+sinRing(d2,a+PI/8.,r2,w);
            }

            void mainImage( out vec4 fragColor, in vec2 fragCoord ){
                // Initializations
                vec3 col = vec3(0);
                vec2 uv = (2.*fragCoord-iResolution.xy) / max(iResolution.x, iResolution.y);
                animTime = 2.133333*iTime;
                float a = atan(uv.x/uv.y); //polar angle (limited)
                float d2 = uv.x*uv.x+uv.y*uv.y; //polar distance squared
                float p,s,amp,pc;

                // Simulated FFT Value Generation
                float baseFreq = 2.0 * PI * iTime;
                fft.x = abs(sin(baseFreq)); // Simulating bass frequency
                fft.y = abs(sin(2.0 * baseFreq)); // Simulating speech frequency
                fft.z = abs(sin(4.0 * baseFreq)); // Simulating presence frequency
                fft.w = abs(sin(8.0 * baseFreq)); // Simulating brilliance frequency
                fft /= vec4(12,36,144,320); // Normalize

                // Geometry
                for (float n=0.;n<8.;n++){
                    p = .01*animTime+n*PI/4.; //phase
                    s = smoothstep(-.3,0.,-cos(p));
                    pc = a+p+animTime; //phase color
                    amp = (.9-.8*s)*sinRing2(d2,(-1.+2.*mod(n,2.))*a,.9*(1.+.999*sin(p)),1.+.5*s); //rings
                    col += amp*(.1+.9*fft.zyw)*vec3(sin(pc),sin(pc+PI/3.),sin(pc+2.*PI/3.)); //colors
                }
                
                // Finalizations
                col *= col; // negative correction & harder falloff
                col += vec3(.001/abs(d2-.0015)*(.1+.9*fft.x)); //center eye
                col = pow(col, vec3(.4545)); //gamma correction
                fragColor = vec4(col, 1.0);
            }
            
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
            material.uniforms.iTime.value += 0.01;
            material.uniforms.iFrame.value += 1;
            composer.render(scene, camera);
        }

        window.addEventListener('resize', () => {
            const width = window.innerWidth;
            const height = window.innerHeight;

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
