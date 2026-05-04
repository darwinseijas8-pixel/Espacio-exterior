<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Creador de Galaxias Mágicas</title>
    <style>
        :root { --primary: #9b59b6; --secondary: #e91e63; --bg: #050509; }
        body { margin: 0; padding: 0; overflow-x: hidden; background-color: var(--bg); font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; color: white; }
        
        /* PANEL DE CONFIGURACIÓN MEJORADO */
        #setup-panel {
            position: relative; margin: 20px auto; width: 90%; max-width: 600px;
            background: rgba(15, 15, 30, 0.95); padding: 30px; border-radius: 25px;
            border: 1px solid var(--primary); box-shadow: 0 0 40px rgba(155, 89, 182, 0.2);
            z-index: 1000;
        }

        h2 { text-align: center; color: var(--primary); margin-bottom: 10px; font-size: 1.8rem; }
        .instrucciones { font-size: 0.85rem; color: #aaa; text-align: center; margin-bottom: 25px; line-height: 1.4; }

        .helper-buttons { display: flex; gap: 10px; justify-content: center; margin-bottom: 20px; flex-wrap: wrap; }
        .btn-help { 
            padding: 8px 15px; font-size: 0.75rem; border-radius: 20px; border: 1px solid #444;
            background: #1a1a2e; color: #ffb3c1; cursor: pointer; text-decoration: none; transition: 0.3s;
        }
        .btn-help:hover { background: var(--primary); color: white; border-color: var(--primary); }

        .section-title { font-size: 0.9rem; font-weight: bold; color: var(--primary); margin: 20px 0 10px; border-bottom: 1px solid #333; padding-bottom: 5px; }

        input[type="text"] {
            width: 100%; padding: 12px; margin-bottom: 10px; border-radius: 10px;
            border: 1px solid #333; background: #000; color: #00ffcc; font-size: 0.9rem;
            box-sizing: border-box; outline: none;
        }
        input[type="text"]:focus { border-color: var(--primary); box-shadow: 0 0 10px rgba(155, 89, 182, 0.3); }

        .foto-card { background: rgba(255,255,255,0.03); padding: 15px; border-radius: 15px; margin-bottom: 15px; border: 1px solid #222; }
        .foto-card label { display: block; font-size: 0.75rem; margin-bottom: 5px; color: #888; }

        .btn-main {
            width: 100%; padding: 18px; background: linear-gradient(45deg, var(--primary), var(--secondary));
            border: none; color: white; font-weight: bold; border-radius: 15px;
            cursor: pointer; font-size: 1.1rem; margin-top: 20px; box-shadow: 0 5px 15px rgba(233, 30, 99, 0.3);
        }

        #link-container {
            margin-top: 25px; display: none; background: #000; padding: 20px;
            border-radius: 15px; border: 2px dashed var(--primary); text-align: center;
        }
        #resultado-link { font-family: monospace; font-size: 0.8rem; color: #00ffcc; word-break: break-all; display: block; margin-top: 10px; padding: 10px; background: #111; border-radius: 5px; }

        /* ELEMENTOS DE LA GALAXIA (IGUAL QUE ANTES) */
        canvas { position: fixed; top: 0; left: 0; z-index: -1; }
        #pantalla-inicio { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #050509; display: flex; flex-direction: column; align-items: center; justify-content: center; z-index: 2000; }
        .overlay-text { position: absolute; left: 50%; transform: translateX(-50%); text-align: center; width: 85%; z-index: 100; pointer-events: none; opacity: 0; transition: opacity 1.5s; }
        #mensaje-inicial { bottom: 50px; }
        #pregunta-final { top: 50%; transform: translate(-50%, -50%); }
        .bubble { background: rgba(0,0,0,0.8); padding: 20px; border-radius: 30px; border: 1px solid var(--primary); font-size: 1.2rem; }
        .pregunta-card { font-size: 2.5rem; color: #ffb3c1; text-shadow: 0 0 20px #ffb3c1; font-weight: bold; }
        .show { opacity: 1 !important; }
    </style>
</head>
<body>

    <audio id="musica-galaxia" crossorigin="anonymous" loop></audio>

    <div id="pantalla-inicio" style="display: none;">
        <h2 id="carga-estado" style="color: white;">Preparando algo mágico...</h2>
        <button id="btn-entrar" onclick="activarTodo()" style="display: none; padding: 15px 40px; border-radius: 50px; border: none; background: var(--primary); color: white; font-weight: bold; cursor: pointer;">ENTRAR ❤️</button>
    </div>

    <div id="setup-panel">
        <h2>🌌 Mi Regalo Galáctico</h2>
        <p class="instrucciones">Sube tus fotos a ImgBB o Postimages, copia el <b>"Enlace Directo"</b> y pégalo abajo. ¡Así de fácil!</p>
        
        <div class="helper-buttons">
            <a href="https://imgbb.com/" target="_blank" class="btn-help">📸 Subir Fotos (ImgBB)</a>
            <a href="https://postimages.org/" target="_blank" class="btn-help">🖼️ Subir Fotos (Postimages)</a>
            <a href="https://www.dropbox.com/" target="_blank" class="btn-help">🎵 Subir MP3 (Dropbox)</a>
        </div>

        <div class="section-title">1. Música y Mensajes</div>
        <input type="text" id="input-audio" placeholder="Link de Música (.mp3)">
        <input type="text" id="input-msg" placeholder="Frase al inicio (ej: Nuestra historia...)">
        <input type="text" id="input-ask" placeholder="Pregunta final (ej: ¿Quieres ser mi novia?)">

        <div class="section-title">2. Tus Fotos (Máximo 10)</div>
        <div id="campos-fotos"></div>

        <button onclick="generarLink()" class="btn-main">✨ CREAR LINK PARA MI NOVIA ✨</button>

        <div id="link-container">
            <p style="margin:0; font-size: 0.8rem;">¡Listo! Copia este link y enviáselo:</p>
            <code id="resultado-link"></code>
            <p style="font-size: 0.7rem; color: #888; margin-top: 10px;">(Púlsalo para seleccionar todo)</p>
        </div>
    </div>

    <div id="mensaje-inicial" class="overlay-text"><div class="bubble" id="text-1"></div></div>
    <div id="pregunta-final" class="overlay-text"><div class="pregunta-card" id="text-2"></div></div>

    <canvas id="bg-canvas"></canvas>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>

    <script>
        let scene, camera, renderer, galaxy, controls, misFotos = [];
        let datosConfig = null;
        let cargaFinalizada = false;

        // Generar los 10 campos de fotos de forma elegante
        const grid = document.getElementById('campos-fotos');
        for(let i=1; i<=10; i++) {
            grid.innerHTML += `
                <div class="foto-card">
                    <label>FOTO #${i}</label>
                    <input type="text" id="u-${i}" placeholder="Pegar URL de imagen aquí (.jpg, .png)">
                    <input type="text" id="t-${i}" placeholder="Frase corta para esta foto (opcional)">
                </div>`;
        }

        // LÓGICA DE CARGA DE DATOS
        const urlParams = new URLSearchParams(window.location.search);
        if(urlParams.has('data')) {
            document.getElementById('setup-panel').style.display = 'none';
            document.getElementById('pantalla-inicio').style.display = 'flex';
            try {
                datosConfig = JSON.parse(decodeURIComponent(escape(atob(urlParams.get('data')))));
                
                // Timeout de seguridad: 3 segundos
                setTimeout(() => { if (!cargaFinalizada) mostrarBoton(); }, 3000);

                const manager = new THREE.LoadingManager();
                manager.onLoad = () => mostrarBoton();
                window.assetLoader = new THREE.TextureLoader(manager);
            } catch(e) { document.getElementById('carga-estado').innerText = "Link no válido"; }
        }

        function mostrarBoton() {
            cargaFinalizada = true;
            document.getElementById('carga-estado').innerText = "¡Todo listo para ti!";
            document.getElementById('btn-entrar').style.display = 'block';
        }

        async function activarTodo() {
            document.getElementById('pantalla-inicio').style.display = 'none';
            if (datosConfig.au) {
                const audio = document.getElementById('musica-galaxia');
                audio.src = datosConfig.au;
                audio.play().catch(() => {});
            }
            if ('wakeLock' in navigator) { try { await navigator.wakeLock.request('screen'); } catch(e) {} }
            iniciarGalaxia(datosConfig);
        }

        function generarLink() {
            const fotos = [];
            for(let i=1; i<=10; i++) {
                const url = document.getElementById(`u-${i}`).value;
                if(url) fotos.push({ u: url, t: document.getElementById(`t-${i}`).value });
            }
            const data = { 
                au: document.getElementById('input-audio').value, 
                m1: document.getElementById('input-msg').value || "Nuestros momentos...", 
                m2: document.getElementById('input-ask').value || "¿Quieres ser mi novia?", 
                f: fotos 
            };
            const base64 = btoa(unescape(encodeURIComponent(JSON.stringify(data))));
            const linkFinal = window.location.origin + window.location.pathname + '?data=' + base64;
            document.getElementById('link-container').style.display = 'block';
            document.getElementById('resultado-link').innerText = linkFinal;
            window.scrollTo(0, document.body.scrollHeight);
        }

        function iniciarGalaxia(config) {
            document.getElementById('text-1').innerText = config.m1;
            document.getElementById('text-2').innerText = config.m2;
            
            scene = new THREE.Scene();
            camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
            camera.position.set(0, 15, 0.1);
            renderer = new THREE.WebGLRenderer({canvas: document.getElementById('bg-canvas'), antialias: true});
            renderer.setSize(window.innerWidth, window.innerHeight);
            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.autoRotate = true; controls.autoRotateSpeed = 0.6; controls.enableZoom = false;

            // Estrellas
            const starGeo = new THREE.BufferGeometry();
            const starPos = [];
            for(let i=0; i<6000; i++) starPos.push((Math.random()-0.5)*1000, (Math.random()-0.5)*1000, (Math.random()-0.5)*1000);
            starGeo.setAttribute('position', new THREE.Float32BufferAttribute(starPos, 3));
            scene.add(new THREE.Points(starGeo, new THREE.PointsMaterial({color: 0xffffff, size: 0.15})));

            // Nebulosa Rosa
            galaxy = new THREE.Group(); scene.add(galaxy);
            for(let i=0; i<3500; i++) {
                const p = new THREE.Mesh(new THREE.SphereGeometry(0.12, 4, 4), new THREE.MeshBasicMaterial({color: 0xffb3c1, transparent: true, opacity: 0.3}));
                const r = Math.random() * 85; const a = Math.random() * Math.PI * 2;
                p.position.set(Math.cos(a)*r, -15, Math.sin(a)*r);
                galaxy.add(p);
            }

            const loader = window.assetLoader || new THREE.TextureLoader();
            config.f.forEach((data, i) => {
                loader.load(data.u, (tex) => {
                    const ang = (i / config.f.length) * Math.PI * 2;
                    const grupo = new THREE.Group();
                    const foto = new THREE.Mesh(new THREE.PlaneGeometry(8, 8), new THREE.MeshBasicMaterial({map: tex, side: 2}));
                    const marco = new THREE.Mesh(new THREE.PlaneGeometry(8.5, 8.5), new THREE.MeshBasicMaterial({color: 0xffffff, side: 2}));
                    marco.position.z = -0.02;
                    grupo.add(foto); grupo.add(marco);
                    grupo.position.set(Math.cos(ang)*35, 0, Math.sin(ang)*35);
                    grupo.lookAt(0, 0, 0); scene.add(grupo); misFotos.push(grupo);
                });
            });

            setTimeout(() => document.getElementById('mensaje-inicial').classList.add('show'), 1500);
            setTimeout(() => document.getElementById('mensaje-inicial').classList.remove('show'), 7000); 
            setTimeout(() => document.getElementById('pregunta-final').classList.add('show'), 14000); 

            function animate() {
                requestAnimationFrame(animate);
                galaxy.rotation.y += 0.0004;
                misFotos.forEach((g, i) => { g.position.y = Math.sin(Date.now()*0.0015 + i) * 0.5; });
                controls.update(); renderer.render(scene, camera);
            }
            animate();
        }
    </script>
</body>
</html>

