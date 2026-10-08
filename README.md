<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Piano Interactivo - Cifrado Americano</title>
    <!-- Tailwind CSS para diseño rápido y moderno -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6; /* bg-gray-100 */
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .piano-container {
            position: relative;
            width: 100%;
            height: 200px;
            background-color: #1f2937; /* bg-gray-800 */
            border-radius: 0.5rem;
            padding: 4px 4px 0 4px; /* Un poco de margen superior y lateral */
            display: flex;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            user-select: none;
        }

        .key-white {
            position: relative;
            background-color: white;
            border: 1px solid #d1d5db;
            border-top: none;
            border-bottom-left-radius: 4px;
            border-bottom-right-radius: 4px;
            z-index: 1;
            cursor: pointer;
            transition: background-color 0.1s;
            box-shadow: 0 4px 2px rgba(0,0,0,0.1);
            display: flex;
            flex-direction: column;
            justify-content: flex-end;
            padding-bottom: 10px;
            align-items: center;
        }

        .key-white:hover {
            background-color: #f9fafb;
        }

        .key-white:active, .key-white.active-play {
            background-color: #e5e7eb;
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.2);
        }

        .key-black {
            position: absolute;
            background-color: #111827; /* gray-900 */
            border-bottom-left-radius: 4px;
            border-bottom-right-radius: 4px;
            z-index: 2;
            cursor: pointer;
            height: 60%;
            box-shadow: 2px 2px 3px rgba(0,0,0,0.3);
            transition: background-color 0.1s;
        }

        .key-black:hover {
            background-color: #1f2937;
        }

        .key-black:active, .key-black.active-play {
            background-color: #374151;
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.5);
        }

        .highlight-root {
            background-color: #4ade80 !important; /* Verde */
            border-color: #22c55e !important;
        }
        
        .highlight-interval {
            background-color: #60a5fa !important; /* Azul */
            border-color: #3b82f6 !important;
        }

        /* Nota para teclas negras iluminadas para mantener contraste */
        .key-black.highlight-root { background-color: #16a34a !important; }
        .key-black.highlight-interval { background-color: #2563eb !important; }

        .note-label {
            font-size: 0.875rem;
            font-weight: 600;
            color: #6b7280;
            pointer-events: none;
        }
    </style>
</head>
<body class="p-4 md:p-8">

    <div class="max-w-4xl w-full bg-white rounded-2xl shadow-xl overflow-hidden flex flex-col border border-gray-200">
        
        <!-- Cabecera -->
        <div class="bg-indigo-600 text-white p-6 text-center">
            <h1 class="text-2xl md:text-3xl font-bold tracking-tight">Decodificador de Cifrado Americano</h1>
            <p class="mt-2 text-indigo-100 text-sm md:text-base">Explora y escucha cómo se construyen los acordes</p>
        </div>

        <!-- Controles -->
        <div class="p-6 grid grid-cols-1 md:grid-cols-3 gap-6 items-end bg-gray-50 border-b border-gray-200">
            <div>
                <label for="root-select" class="block text-sm font-medium text-gray-700 mb-2">Nota Base (Raíz)</label>
                <select id="root-select" class="w-full bg-white border border-gray-300 text-gray-900 text-base rounded-lg focus:ring-indigo-500 focus:border-indigo-500 block p-2.5 shadow-sm">
                    <option value="0">C (Do)</option>
                    <option value="2">D (Re)</option>
                    <option value="4">E (Mi)</option>
                    <option value="5">F (Fa)</option>
                    <option value="7">G (Sol)</option>
                    <option value="9">A (La)</option>
                    <option value="11">B (Si)</option>
                </select>
            </div>
            
            <div>
                <label for="type-select" class="block text-sm font-medium text-gray-700 mb-2">Tipo de Acorde</label>
                <select id="type-select" class="w-full bg-white border border-gray-300 text-gray-900 text-base rounded-lg focus:ring-indigo-500 focus:border-indigo-500 block p-2.5 shadow-sm">
                    <option value="major">Mayor</option>
                    <option value="minor">Menor (m)</option>
                </select>
            </div>

            <button id="play-chord-btn" class="w-full text-white bg-indigo-600 hover:bg-indigo-700 focus:ring-4 focus:ring-indigo-300 font-medium rounded-lg text-base px-5 py-2.5 text-center transition-colors shadow-md flex justify-center items-center gap-2">
                <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM9.555 7.168A1 1 0 008 8v4a1 1 0 001.555.832l3-2a1 1 0 000-1.664l-3-2z" clip-rule="evenodd"></path></svg>
                Reproducir Acorde
            </button>
        </div>

        <div class="px-6 py-4 flex flex-col md:flex-row justify-between items-center bg-white">
            <div class="flex items-center gap-4 text-sm font-medium text-gray-600 mb-4 md:mb-0">
                <div class="flex items-center gap-1"><span class="w-4 h-4 bg-green-400 rounded-sm inline-block border border-green-500"></span> Raíz</div>
                <div class="flex items-center gap-1"><span class="w-4 h-4 bg-blue-400 rounded-sm inline-block border border-blue-500"></span> 3ra y 5ta</div>
            </div>
            <div class="text-lg font-semibold text-gray-800 bg-gray-100 px-4 py-2 rounded-lg border border-gray-200 min-w-[200px] text-center" id="status-display">
                Toca una tecla...
            </div>
        </div>

        <!-- Contenedor del Piano -->
        <div class="p-6 pt-0">
            <div id="piano" class="piano-container">
                <!-- Las teclas se generarán por JavaScript -->
            </div>
        </div>
    </div>

    <script>
        // Datos de 24 teclas (2 octavas: C3 a B4)
        const keysData = [
            { note: 'C', cipher: 'C', octave: 3, type: 'white', freq: 130.81, id: 0, afterW: -1 },
            { note: 'C#', cipher: 'C#', octave: 3, type: 'black', freq: 138.59, id: 1, afterW: 0 },
            { note: 'D', cipher: 'D', octave: 3, type: 'white', freq: 146.83, id: 2, afterW: -1 },
            { note: 'D#', cipher: 'D#', octave: 3, type: 'black', freq: 155.56, id: 3, afterW: 1 },
            { note: 'E', cipher: 'E', octave: 3, type: 'white', freq: 164.81, id: 4, afterW: -1 },
            { note: 'F', cipher: 'F', octave: 3, type: 'white', freq: 174.61, id: 5, afterW: -1 },
            { note: 'F#', cipher: 'F#', octave: 3, type: 'black', freq: 185.00, id: 6, afterW: 3 },
            { note: 'G', cipher: 'G', octave: 3, type: 'white', freq: 196.00, id: 7, afterW: -1 },
            { note: 'G#', cipher: 'G#', octave: 3, type: 'black', freq: 207.65, id: 8, afterW: 4 },
            { note: 'A', cipher: 'A', octave: 3, type: 'white', freq: 220.00, id: 9, afterW: -1 },
            { note: 'A#', cipher: 'A#', octave: 3, type: 'black', freq: 233.08, id: 10, afterW: 5 },
            { note: 'B', cipher: 'B', octave: 3, type: 'white', freq: 246.94, id: 11, afterW: -1 },
            // Octava 4
            { note: 'C', cipher: 'C', octave: 4, type: 'white', freq: 261.63, id: 12, afterW: -1 },
            { note: 'C#', cipher: 'C#', octave: 4, type: 'black', freq: 277.18, id: 13, afterW: 7 },
            { note: 'D', cipher: 'D', octave: 4, type: 'white', freq: 293.66, id: 14, afterW: -1 },
            { note: 'D#', cipher: 'D#', octave: 4, type: 'black', freq: 311.13, id: 15, afterW: 8 },
            { note: 'E', cipher: 'E', octave: 4, type: 'white', freq: 329.63, id: 16, afterW: -1 },
            { note: 'F', cipher: 'F', octave: 4, type: 'white', freq: 349.23, id: 17, afterW: -1 },
            { note: 'F#', cipher: 'F#', octave: 4, type: 'black', freq: 369.99, id: 18, afterW: 10 },
            { note: 'G', cipher: 'G', octave: 4, type: 'white', freq: 392.00, id: 19, afterW: -1 },
            { note: 'G#', cipher: 'G#', octave: 4, type: 'black', freq: 415.30, id: 20, afterW: 11 },
            { note: 'A', cipher: 'A', octave: 4, type: 'white', freq: 440.00, id: 21, afterW: -1 },
            { note: 'A#', cipher: 'A#', octave: 4, type: 'black', freq: 466.16, id: 22, afterW: 12 },
            { note: 'B', cipher: 'B', octave: 4, type: 'white', freq: 493.88, id: 23, afterW: -1 }
        ];

        let audioContext = null;
        const pianoContainer = document.getElementById('piano');
        const rootSelect = document.getElementById('root-select');
        const typeSelect = document.getElementById('type-select');
        const playBtn = document.getElementById('play-chord-btn');
        const statusDisplay = document.getElementById('status-display');
        let currentChordIndices = [];

        // Inicializar Audio Context solo tras interacción del usuario (políticas del navegador)
        function initAudio() {
            if (!audioContext) {
                audioContext = new (window.AudioContext || window.webkitAudioContext)();
            }
            if (audioContext.state === 'suspended') {
                audioContext.resume();
            }
        }

        function playTone(frequency, isChord = false) {
            initAudio();
            const osc = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            
            // Usamos onda triangular que suena suave, parecido a un piano/synth suave
            osc.type = 'triangle';
            
            osc.connect(gainNode);
            gainNode.connect(audioContext.destination);
            osc.frequency.value = frequency;
            
            // Envolvente de volumen (ADSR)
            const now = audioContext.currentTime;
            gainNode.gain.setValueAtTime(0, now);
            // Reducir volumen general si es un acorde para evitar saturación
            const maxVol = isChord ? 0.3 : 0.6;
            gainNode.gain.linearRampToValueAtTime(maxVol, now + 0.05); // Attack
            gainNode.gain.exponentialRampToValueAtTime(0.001, now + 1.5); // Decay/Release
            
            osc.start(now);
            osc.stop(now + 1.5);
        }

        function renderKeyboard() {
            pianoContainer.innerHTML = '';
            
            const totalWhites = 14; // 7 notas naturales * 2 octavas
            const whiteKeyWidth = 100 / totalWhites;
            const blackKeyWidth = 4.5; // Porcentaje del contenedor
            
            let whiteKeysCount = 0;

            keysData.forEach((key) => {
                const keyEl = document.createElement('div');
                keyEl.id = `key-${key.id}`;
                
                if (key.type === 'white') {
                    keyEl.className = 'key-white';
                    keyEl.style.width = `${whiteKeyWidth}%`;
                    
                    // Solo poner letra en las notas base C y F para referencia visual rápida
                    if (key.note === 'C' || key.note === 'F') {
                        const label = document.createElement('span');
                        label.className = 'note-label';
                        label.innerText = key.cipher;
                        keyEl.appendChild(label);
                    }
                    whiteKeysCount++;
                } else {
                    keyEl.className = 'key-black';
                    keyEl.style.width = `${blackKeyWidth}%`;
                    // Calcular posición basada en la tecla blanca que le precede
                    const leftPos = ((key.afterW + 1) * whiteKeyWidth) - (blackKeyWidth / 2);
                    keyEl.style.left = `${leftPos}%`;
                }

                // Eventos de interacción
                keyEl.addEventListener('mousedown', () => handleKeyPress(key));
                keyEl.addEventListener('touchstart', (e) => { e.preventDefault(); handleKeyPress(key); });

                pianoContainer.appendChild(keyEl);
            });
        }

        function updateChordHighlights() {
            // Limpiar iluminaciones previas
            document.querySelectorAll('.key-white, .key-black').forEach(el => {
                el.classList.remove('highlight-root', 'highlight-interval');
            });

            const rootIndex = parseInt(rootSelect.value);
            const type = typeSelect.value;
            
            // Fórmulas de semitonos (Tónica, 3ra, 5ta)
            // Mayor: 4 semitonos, 7 semitonos.
            // Menor: 3 semitonos, 7 semitonos.
            const thirdOffset = (type === 'major') ? 4 : 3;
            const fifthOffset = 7;

            currentChordIndices = [rootIndex, rootIndex + thirdOffset, rootIndex + fifthOffset];

            // Aplicar colores
            const rootEl = document.getElementById(`key-${currentChordIndices[0]}`);
            const thirdEl = document.getElementById(`key-${currentChordIndices[1]}`);
            const fifthEl = document.getElementById(`key-${currentChordIndices[2]}`);

            if (rootEl) rootEl.classList.add('highlight-root');
            if (thirdEl) thirdEl.classList.add('highlight-interval');
            if (fifthEl) fifthEl.classList.add('highlight-interval');
            
            // Actualizar etiqueta
            const rootName = keysData[rootIndex].cipher;
            const suffix = type === 'minor' ? 'm' : '';
            const chordName = `${rootName}${suffix}`;
            statusDisplay.innerText = `Acorde Formado: ${chordName}`;
        }

        function handleKeyPress(keyObj) {
            playTone(keyObj.freq, false);
            statusDisplay.innerText = `Nota Individual: ${keyObj.cipher}`;
            
            // Animación de presionado
            const el = document.getElementById(`key-${keyObj.id}`);
            el.classList.add('active-play');
            setTimeout(() => el.classList.remove('active-play'), 150);
        }

        function playCurrentChord() {
            if (currentChordIndices.length === 3) {
                currentChordIndices.forEach(idx => {
                    const keyObj = keysData[idx];
                    if (keyObj) {
                        playTone(keyObj.freq, true);
                        
                        // Animación de presionado múltiple
                        const el = document.getElementById(`key-${idx}`);
                        el.classList.add('active-play');
                        setTimeout(() => el.classList.remove('active-play'), 200);
                    }
                });
            }
        }

        // Configurar Listeners
        rootSelect.addEventListener('change', updateChordHighlights);
        typeSelect.addEventListener('change', updateChordHighlights);
        playBtn.addEventListener('click', () => {
            initAudio(); // Asegurar context
            playCurrentChord();
        });

        // Iniciar
        renderKeyboard();
        updateChordHighlights();

    </script>
</body>
</html>
