
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reator de Fusão Nuclear Comercial Tokamak - Simulação Mk-IV</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Fontes Sci-Fi do Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600;700;900&family=Rajdhani:wght@500;600;700&family=Share+Tech+Mono&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --bg-dark: #040814;
            --panel-bg: rgba(8, 15, 30, 0.75);
            --neon-cyan: #00f0ff;
            --neon-magenta: #ff007f;
            --neon-amber: #ffaa00;
            --neon-green: #00ff66;
            --neon-red: #ff2a2a;
            --glass-border: rgba(0, 240, 255, 0.25);
        }

        body {
            background-color: var(--bg-dark);
            color: #e2e8f0;
            font-family: 'Rajdhani', sans-serif;
            overflow-x: hidden;
            background-image: 
                radial-gradient(circle at 50% 50%, rgba(0, 240, 255, 0.05) 0%, transparent 80%),
                linear-gradient(to bottom, rgba(4, 8, 20, 0.9), rgba(4, 8, 20, 0.98));
        }

        .font-orbitron { font-family: 'Orbitron', sans-serif; }
        .font-mono-tech { font-family: 'Share Tech Mono', monospace; }

        /* Estilo Painel de Vidro Glossmorphism */
        .glass-panel {
            background: var(--panel-bg);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid var(--glass-border);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.6), inset 0 0 12px rgba(0, 240, 255, 0.05);
            border-radius: 12px;
        }

        .glass-panel-glow {
            border-color: rgba(0, 240, 255, 0.5);
            box-shadow: 0 0 20px rgba(0, 240, 255, 0.2), inset 0 0 15px rgba(0, 240, 255, 0.1);
        }

        /* Efeitos Glow Neon */
        .glow-cyan { text-shadow: 0 0 10px rgba(0, 240, 255, 0.7); }
        .glow-amber { text-shadow: 0 0 10px rgba(ff, 170, 0, 0.7); }
        .glow-magenta { text-shadow: 0 0 10px rgba(ff, 0, 127, 0.7); }
        .glow-red { text-shadow: 0 0 10px rgba(255, 42, 42, 0.8); }

        /* Custom Sliders */
        input[type=range] {
            -webkit-appearance: none;
            background: rgba(15, 23, 42, 0.8);
            border: 1px solid rgba(0, 240, 255, 0.3);
            height: 6px;
            border-radius: 3px;
            outline: none;
        }

        input[type=range]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 18px;
            height: 18px;
            border-radius: 50%;
            background: var(--neon-cyan);
            cursor: pointer;
            box-shadow: 0 0 10px var(--neon-cyan);
            border: 2px solid #ffffff;
            transition: transform 0.1s;
        }

        input[type=range]::-webkit-slider-thumb:hover {
            transform: scale(1.2);
        }

        /* Botões Sci-Fi */
        .btn-sci-fi {
            position: relative;
            background: linear-gradient(135deg, rgba(0, 240, 255, 0.1) 0%, rgba(0, 100, 200, 0.2) 100%);
            border: 1px solid var(--neon-cyan);
            color: #ffffff;
            transition: all 0.2s ease;
            clip-path: polygon(8px 0, 100% 0, 100% calc(100% - 8px), calc(100% - 8px) 100%, 0 100%, 0 8px);
        }

        .btn-sci-fi:hover {
            background: linear-gradient(135deg, rgba(0, 240, 255, 0.3) 0%, rgba(0, 150, 255, 0.4) 100%);
            box-shadow: 0 0 15px rgba(0, 240, 255, 0.5);
            transform: translateY(-1px);
        }

        .btn-sci-fi:active {
            transform: translateY(1px);
        }

        .btn-danger {
            background: linear-gradient(135deg, rgba(255, 42, 42, 0.2) 0%, rgba(150, 0, 0, 0.4) 100%);
            border-color: var(--neon-red);
            color: #ffffff;
        }

        .btn-danger:hover {
            background: linear-gradient(135deg, rgba(255, 42, 42, 0.5) 0%, rgba(200, 0, 0, 0.6) 100%);
            box-shadow: 0 0 20px rgba(255, 42, 42, 0.7);
        }

        /* Animações e Pulsações */
        @keyframes pulse-slow {
            0%, 100% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.7; transform: scale(0.98); }
        }

        .animate-pulse-slow {
            animation: pulse-slow 3s infinite ease-in-out;
        }

        /* Custom Scrollbars */
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: rgba(5, 10, 20, 0.8); }
        ::-webkit-scrollbar-thumb { background: rgba(0, 240, 255, 0.3); border-radius: 3px; }
        ::-webkit-scrollbar-thumb:hover { background: var(--neon-cyan); }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between p-3 lg:p-5 text-sm select-none">

    <!-- CABAÇALHO DA INTERFACE -->
    <header class="glass-panel p-4 mb-4 flex flex-col md:flex-row justify-between items-center gap-4 relative overflow-hidden">
        <div class="absolute -top-10 -left-10 w-32 h-32 bg-cyan-500/10 rounded-full blur-2xl pointer-events-none"></div>
        <div class="flex items-center gap-3 z-10">
            <div class="w-10 h-10 rounded-lg bg-cyan-950/80 border border-cyan-400/50 flex items-center justify-center shadow-lg shadow-cyan-500/20">
                <svg class="w-6 h-6 text-cyan-400 animate-spin" style="animation-duration: 12s;" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <circle cx="12" cy="12" r="9" stroke-width="1.5" stroke-dasharray="4 2"></circle>
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v3m0 12v3m9-9h-3M6 12H3m15.364-6.364l-2.121 2.121M7.757 16.243l-2.121 2.121m12.728 0l-2.121-2.121M7.757 7.757L5.636 5.636"></path>
                </svg>
            </div>
            <div>
                <h1 class="font-orbitron font-extrabold text-xl md:text-2xl tracking-wider text-white flex items-center gap-2">
                    REATOR DE FUSÃO TOKAMAK <span class="text-xs px-2 py-0.5 rounded bg-cyan-900/60 border border-cyan-400/40 text-cyan-300 font-mono-tech">MK-IV COMERCIAL</span>
                </h1>
                <p class="text-xs text-cyan-200/70 font-mono-tech">SISTEMA DE CONTROLE DE PLASMA TERMONUCLEAR & CONVERÇÃO DE ENERGIA D-T</p>
            </div>
        </div>

        <!-- BADGES DE STATUS -->
        <div class="flex items-center gap-3 z-10">
            <div id="statusBadge" class="flex items-center gap-2 px-3 py-1.5 rounded-full bg-emerald-950/60 border border-emerald-500/40 text-emerald-400 font-orbitron text-xs tracking-wide">
                <span class="w-2.5 h-2.5 rounded-full bg-emerald-400 animate-ping"></span>
                <span id="statusText">OPERAÇÃO ESTÁVEL</span>
            </div>
            <div id="qFactorBadge" class="flex flex-col items-end px-3 py-1 rounded bg-slate-900/80 border border-cyan-500/30">
                <span class="text-[10px] text-cyan-400/80 uppercase tracking-widest font-mono-tech">Fator de Ganho (Q)</span>
                <span id="qFactorVal" class="font-orbitron font-bold text-lg text-cyan-300 glow-cyan">0.00</span>
            </div>
        </div>
    </header>

    <!-- CONTAINER PRINCIPAL (GRID DASHBOARD) -->
    <main class="grid grid-cols-1 lg:grid-cols-12 gap-4 flex-1">

        <!-- COLUNA ESQUERDA: CANVAS DO TOKAMAK & CONTROLES VISUAIS (8 COLS) -->
        <section class="lg:col-span-8 flex flex-col gap-4">
            <!-- CANVAS CONTAINER -->
            <div class="glass-panel p-2 relative flex-1 min-h-[420px] lg:min-h-[520px] flex items-center justify-center overflow-hidden">
                <canvas id="tokamakCanvas" class="w-full h-full block rounded-lg cursor-crosshair"></canvas>
                
                <!-- HUD OVERLAY NO CANVAS -->
                <div class="absolute top-4 left-4 pointer-events-none flex flex-col gap-1 text-xs font-mono-tech">
                    <div class="bg-slate-950/70 backdrop-blur-md border border-cyan-500/30 px-2.5 py-1 rounded text-cyan-300 flex items-center gap-2">
                        <span class="w-2 h-2 rounded-full bg-cyan-400"></span>
                        CAMPO MAGNÉTICO: <span id="hudBField" class="font-bold text-white">8.5 T</span>
                    </div>
                    <div class="bg-slate-950/70 backdrop-blur-md border border-amber-500/30 px-2.5 py-1 rounded text-amber-300 flex items-center gap-2">
                        <span class="w-2 h-2 rounded-full bg-amber-400"></span>
                        DENSIDADE DO PLASMA: <span id="hudDensity" class="font-bold text-white">1.0 ×10²⁰ m⁻³</span>
                    </div>
                </div>

                <div class="absolute top-4 right-4 pointer-events-none flex flex-col gap-1 text-xs font-mono-tech items-end">
                    <div class="bg-slate-950/70 backdrop-blur-md border border-purple-500/30 px-2.5 py-1 rounded text-purple-300">
                        REACIONAL: <span class="text-white font-bold">²H + ³H ➔ ⁴He (3.5MeV) + n⁰ (14.1MeV)</span>
                    </div>
                </div>

                <!-- ALERTA DE DISRUPÇÃO (OVERLAY OCULTO POR PADRÃO) -->
                <div id="disruptionOverlay" class="absolute inset-0 bg-red-950/80 backdrop-blur-lg flex flex-col items-center justify-center text-center p-6 gap-4 hidden z-20">
                    <div class="w-16 h-16 rounded-full bg-red-600/30 border-2 border-red-500 flex items-center justify-center animate-bounce">
                        <svg class="w-10 h-10 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"></path></svg>
                    </div>
                    <div>
                        <h2 class="font-orbitron font-extrabold text-2xl text-red-400 glow-red mb-1">DISRUPÇÃO DE PLASMA DETECTADA!</h2>
                        <p class="text-sm text-red-200/80 max-w-md">Instabilidade Magneto-Hidrodinâmica (MHD) excedeu a pressão de confinamento. O plasma colidiu com a parede do vaso de vácuo.</p>
                    </div>
                    <button id="btnResetDisruption" class="btn-sci-fi btn-danger px-6 py-2.5 rounded font-orbitron font-bold tracking-wider text-sm flex items-center gap-2">
                        REINICIAR SISTEMA & PURGAR CAMA
                    </button>
                </div>
            </div>

            <!-- LEGENDA VISUAL DA REAÇÃO DE FUSÃO D-T -->
            <div class="glass-panel p-3 grid grid-cols-2 sm:grid-cols-4 gap-2 text-xs font-mono-tech">
                <div class="flex items-center gap-2 bg-slate-900/50 p-1.5 rounded border border-cyan-500/20">
                    <span class="w-3 h-3 rounded-full bg-cyan-400 shadow-sm shadow-cyan-400"></span>
                    <span class="text-slate-300">Deutério (²H - Azul)</span>
                </div>
                <div class="flex items-center gap-2 bg-slate-900/50 p-1.5 rounded border border-emerald-500/20">
                    <span class="w-3 h-3 rounded-full bg-emerald-400 shadow-sm shadow-emerald-400"></span>
                    <span class="text-slate-300">Trítio (³H - Verde)</span>
                </div>
                <div class="flex items-center gap-2 bg-slate-900/50 p-1.5 rounded border border-amber-500/20">
                    <span class="w-3 h-3 rounded-full bg-amber-400 shadow-sm shadow-amber-400"></span>
                    <span class="text-slate-300">Partícula Alfa (⁴He)</span>
                </div>
                <div class="flex items-center gap-2 bg-slate-900/50 p-1.5 rounded border border-rose-500/20">
                    <span class="w-3 h-3 stroke-white border border-white/60 bg-white shadow-sm shadow-white"></span>
                    <span class="text-slate-300">Nêutron (14.1 MeV)</span>
                </div>
            </div>
        </section>

        <!-- COLUNA DIREITA: PAINEL DE CONTROLE E MÉTRICAS (4 COLS) -->
        <section class="lg:col-span-4 flex flex-col gap-4">
            
            <!-- MÉTRICAS PRINCIPAIS EM TEMPO REAL -->
            <div class="glass-panel p-4 flex flex-col gap-3">
                <h2 class="font-orbitron font-bold text-sm text-cyan-400 tracking-wider flex items-center justify-between border-b border-cyan-500/20 pb-2">
                    TELEMETRIA DO PLASMA
                    <span class="text-[10px] text-slate-400 font-mono-tech">SISTEMA ATIVO</span>
                </h2>

                <div class="grid grid-cols-2 gap-3">
                    <!-- Temperatura -->
                    <div class="bg-slate-950/60 p-2.5 rounded border border-slate-800">
                        <span class="text-[10px] text-slate-400 uppercase font-mono-tech block mb-1">Temp. Plasma (T)</span>
                        <div class="flex items-baseline gap-1">
                            <span id="metricTemp" class="font-orbitron font-extrabold text-xl text-amber-400 glow-amber">15.0</span>
                            <span class="text-xs text-amber-300/80 font-mono-tech">M °C</span>
                        </div>
                    </div>

                    <!-- Potência Líquida -->
                    <div class="bg-slate-950/60 p-2.5 rounded border border-slate-800">
                        <span class="text-[10px] text-slate-400 uppercase font-mono-tech block mb-1">Potência Líquida</span>
                        <div class="flex items-baseline gap-1">
                            <span id="metricNetPower" class="font-orbitron font-extrabold text-xl text-cyan-400 glow-cyan">0</span>
                            <span id="metricNetPowerUnit" class="text-xs text-cyan-300/80 font-mono-tech">MW</span>
                        </div>
                    </div>

                    <!-- Potência Fusão -->
                    <div class="bg-slate-950/60 p-2.5 rounded border border-slate-800">
                        <span class="text-[10px] text-slate-400 uppercase font-mono-tech block mb-1">Potência Fusão</span>
                        <div class="flex items-baseline gap-1">
                            <span id="metricFusionPower" class="font-orbitron font-bold text-base text-purple-300">0.0</span>
                            <span class="text-xs text-purple-400/80 font-mono-tech">MW</span>
                        </div>
                    </div>

                    <!-- Estabilidade -->
                    <div class="bg-slate-950/60 p-2.5 rounded border border-slate-800">
                        <span class="text-[10px] text-slate-400 uppercase font-mono-tech block mb-1">Estabilidade Campo</span>
                        <div class="flex items-baseline gap-1">
                            <span id="metricStability" class="font-orbitron font-bold text-base text-emerald-400">100</span>
                            <span class="text-xs text-emerald-300/80 font-mono-tech">%</span>
                        </div>
                    </div>
                </div>

                <!-- CRITÉRIO DE LAWSON (PRODUTO TRIPLO) -->
                <div class="bg-slate-950/80 p-3 rounded border border-cyan-500/30 flex flex-col gap-1.5">
                    <div class="flex justify-between items-center text-xs font-mono-tech">
                        <span class="text-cyan-300">Produto Triplo de Lawson (n·T·τE)</span>
                        <span id="lawsonVal" class="text-cyan-400 font-bold">0.12 / 3.00</span>
                    </div>
                    <div class="w-full bg-slate-900 h-2 rounded-full overflow-hidden border border-slate-800">
                        <div id="lawsonBar" class="bg-gradient-to-r from-cyan-500 via-amber-400 to-purple-500 h-full transition-all duration-300" style="width: 4%;"></div>
                    </div>
                    <p id="lawsonDesc" class="text-[10px] text-slate-400 font-mono-tech">Abaixo do limiar de ignição autossustentada.</p>
                </div>
            </div>

            <!-- PAINEL DE CONTROLE (SLIDERS) -->
            <div class="glass-panel p-4 flex flex-col gap-4">
                <h2 class="font-orbitron font-bold text-sm text-cyan-400 tracking-wider border-b border-cyan-500/20 pb-2">
                    PARÂMETROS DE OPERAÇÃO
                </h2>

                <!-- Slider 1: Aquecimento Auxiliar -->
                <div class="flex flex-col gap-1">
                    <div class="flex justify-between text-xs font-mono-tech">
                        <label for="inputAuxPower" class="text-slate-300">Aquecimento Auxiliar (NBI / RF)</label>
                        <span id="valAuxPower" class="text-cyan-400 font-bold">40 MW</span>
                    </div>
                    <input type="range" id="inputAuxPower" min="0" max="100" value="40" step="1">
                </div>

                <!-- Slider 2: Campo Magnético -->
                <div class="flex flex-col gap-1">
                    <div class="flex justify-between text-xs font-mono-tech">
                        <label for="inputBField" class="text-slate-300">Campo Magnético de Confinamento</label>
                        <span id="valBField" class="text-cyan-400 font-bold">8.5 Tesla</span>
                    </div>
                    <input type="range" id="inputBField" min="2.0" max="15.0" value="8.5" step="0.1">
                </div>

                <!-- Slider 3: Injeção de Combustível -->
                <div class="flex flex-col gap-1">
                    <div class="flex justify-between text-xs font-mono-tech">
                        <label for="inputFuelRate" class="text-slate-300">Injeção de Combustível D-T</label>
                        <span id="valFuelRate" class="text-cyan-400 font-bold">50 Pellets/s</span>
                    </div>
                    <input type="range" id="inputFuelRate" min="5" max="100" value="50" step="1">
                </div>

                <!-- BOTÃO DE EMERGÊNCIA -->
                <button id="btnPanic" class="btn-sci-fi btn-danger w-full py-2.5 rounded font-orbitron font-bold text-xs tracking-wider flex items-center justify-center gap-2 mt-1">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M18.364 18.364A9 9 0 005.636 5.636m12.728 12.728A9 9 0 015.636 5.636m12.728 12.728L5.636 5.636"></path></svg>
                    VENTILAÇÃO DE EMERGÊNCIA (PURGA DE PLASMA)
                </button>
            </div>

            <!-- PRESETS DE CONFIGURAÇÃO -->
            <div class="glass-panel p-4 flex flex-col gap-2">
                <h2 class="font-orbitron font-bold text-xs text-slate-400 uppercase tracking-wider mb-1">
                    PRESETS DE OPERAÇÃO
                </h2>
                <div class="grid grid-cols-2 gap-2 font-orbitron text-[11px]">
                    <button id="presetStandby" class="btn-sci-fi py-2 px-2 rounded text-slate-200">
                        1. REPOUSO
                    </button>
                    <button id="presetBreakeven" class="btn-sci-fi py-2 px-2 rounded text-amber-300">
                        2. BREAK-EVEN (Q=1)
                    </button>
                    <button id="presetIgnition" class="btn-sci-fi py-2 px-2 rounded text-cyan-300 border-cyan-400/80">
                        3. IGNIÇÃO (Q>20)
                    </button>
                    <button id="presetStress" class="btn-sci-fi py-2 px-2 rounded text-rose-400 border-rose-500/50">
                        4. TESTE ESTABILIDADE
                    </button>
                </div>
            </div>

        </section>
    </main>

    <!-- RODAPÉ DA REDE ELÉTRICA COMERCIAL DA CIDADE FUTURISTA -->
    <footer class="glass-panel p-3 mt-4 flex flex-col sm:flex-row items-center justify-between gap-3 font-mono-tech text-xs">
        <div class="flex items-center gap-3">
            <div class="w-8 h-8 rounded bg-emerald-950 border border-emerald-500/40 flex items-center justify-center">
                <svg class="w-5 h-5 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path></svg>
            </div>
            <div>
                <span class="text-slate-400 uppercase block text-[10px]">Alimentação da Rede Elétrica Metropolitana</span>
                <span id="cityHousesVal" class="font-orbitron font-bold text-sm text-emerald-400 glow-cyan">0 Residências Abastecidas</span>
            </div>
        </div>

        <!-- BARRA DE FLUXO DE ENERGIA DO BLANKET DE LÍTIO -->
        <div class="flex-1 max-w-md w-full bg-slate-950/80 p-2 rounded border border-slate-800 flex items-center gap-2">
            <span class="text-[10px] text-slate-400 shrink-0">TROCADOR TÉRMICO (LÍTIO)</span>
            <div class="flex-1 h-2 bg-slate-900 rounded-full overflow-hidden border border-slate-800 relative">
                <div id="blanketHeatBar" class="h-full bg-gradient-to-r from-amber-600 via-rose-500 to-cyan-400 transition-all duration-300" style="width: 0%;"></div>
            </div>
            <span id="blanketTempText" class="text-[11px] font-bold text-amber-400 shrink-0">25°C</span>
        </div>

        <div class="text-slate-500 text-[11px] text-right">
            SIMULAÇÃO DE FUSÃO TERMONUCLEAR v4.2<br>
            <span class="text-cyan-400/80">LABORATÓRIO NACIONAL DE ENERGIA</span>
        </div>
    </footer>

    <script>
        /* MOTOR PRINCIPAL DA SIMULAÇÃO DE FUSÃO NUCLEAR TOKAMAK */
        (function() {
            // Referências DOM
            const canvas = document.getElementById('tokamakCanvas');
            const ctx = canvas.getContext('2d');

            // Sliders & Controles UI
            const inputAuxPower = document.getElementById('inputAuxPower');
            const inputBField = document.getElementById('inputBField');
            const inputFuelRate = document.getElementById('inputFuelRate');
            
            const valAuxPower = document.getElementById('valAuxPower');
            const valBField = document.getElementById('valBField');
            const valFuelRate = document.getElementById('valFuelRate');

            // Métricas UI
            const metricTemp = document.getElementById('metricTemp');
            const metricNetPower = document.getElementById('metricNetPower');
            const metricNetPowerUnit = document.getElementById('metricNetPowerUnit');
            const metricFusionPower = document.getElementById('metricFusionPower');
            const metricStability = document.getElementById('metricStability');
            const qFactorVal = document.getElementById('qFactorVal');
            const qFactorBadge = document.getElementById('qFactorBadge');
            const lawsonVal = document.getElementById('lawsonVal');
            const lawsonBar = document.getElementById('lawsonBar');
            const lawsonDesc = document.getElementById('lawsonDesc');
            const statusBadge = document.getElementById('statusBadge');
            const statusText = document.getElementById('statusText');
            const hudBField = document.getElementById('hudBField');
            const hudDensity = document.getElementById('hudDensity');
            const cityHousesVal = document.getElementById('cityHousesVal');
            const blanketHeatBar = document.getElementById('blanketHeatBar');
            const blanketTempText = document.getElementById('blanketTempText');
            const disruptionOverlay = document.getElementById('disruptionOverlay');

            // Botões Presets & Ações
            const btnPanic = document.getElementById('btnPanic');
            const btnResetDisruption = document.getElementById('btnResetDisruption');
            const presetStandby = document.getElementById('presetStandby');
            const presetBreakeven = document.getElementById('presetBreakeven');
            const presetIgnition = document.getElementById('presetIgnition');
            const presetStress = document.getElementById('presetStress');

            // ESTADO DA SIMULAÇÃO
            const simState = {
                // Parâmetros do Usuário
                auxHeatingMW: 40,      // 0 a 100 MW
                magneticFieldT: 8.5,   // 2.0 a 15.0 Tesla
                fuelPelletsPerSec: 50, // 5 a 100 pellets/s

                // Parâmetros Físicos Calculados
                plasmaTempMdegC: 15.0, // Milhões de °C
                density: 1.0,          // 10^20 m^-3
                fusionPowerMW: 0.0,
                qFactor: 0.0,
                netPowerMW: 0.0,
                stabilityPct: 100.0,
                lawsonTripleProduct: 0.0,
                blanketTempC: 25.0,
                isDisrupted: false,

                // Animação & Geometria
                particles: [],
                neutronSparks: [],
                magneticFieldOffset: 0,
                time: 0
            };

            // Ajuste do tamanho do Canvas com tratamento de DPI
            function resizeCanvas() {
                const rect = canvas.getBoundingClientRect();
                canvas.width = rect.width * window.devicePixelRatio;
                canvas.height = rect.height * window.devicePixelRatio;
                ctx.scale(window.devicePixelRatio, window.devicePixelRatio);
            }
            window.addEventListener('resize', resizeCanvas);
            resizeCanvas();

            // CLASSE PARA PARTÍCULAS NO PLASMA (D, T, Alpha, Neutrons)
            class PlasmaParticle {
                constructor(type, cx, cy, majorR, minorR) {
                    this.type = type; // 'deut' (²H), 'trit' (³H), 'alpha' (⁴He), 'neutron' (n⁰)
                    this.angle = Math.random() * Math.PI * 2;
                    this.poloidalAngle = Math.random() * Math.PI * 2;
                    this.speed = 0.02 + Math.random() * 0.03;
                    this.poloidalSpeed = 0.05 + Math.random() * 0.05;
                    this.rOffset = (Math.random() - 0.5) * (minorR * 0.7);
                    
                    // Para nêutrons de alta energia escapando
                    if (type === 'neutron') {
                        this.x = cx + Math.cos(this.angle) * majorR;
                        this.y = cy + Math.sin(this.angle) * (minorR * 0.6);
                        const escapeAngle = Math.random() * Math.PI * 2;
                        this.vx = Math.cos(escapeAngle) * (4 + Math.random() * 3);
                        this.vy = Math.sin(escapeAngle) * (4 + Math.random() * 3);
                        this.life = 1.0;
                    }
                }

                update(cx, cy, majorR, minorR, tempRatio) {
                    if (this.type === 'neutron') {
                        this.x += this.vx;
                        this.y += this.vy;
                        this.life -= 0.03;
                        return;
                    }

                    // Órbita Helicoidal ao redor do Toro Tokamak
                    const speedMultiplier = 0.5 + tempRatio * 1.5;
                    this.angle += this.speed * speedMultiplier;
                    this.poloidalAngle += this.poloidalSpeed * speedMultiplier;

                    const currentMinorR = (minorR * 0.6) + Math.sin(this.poloidalAngle) * 12 + this.rOffset;
                    
                    // Posição no plano toroidal projetado 2.5D
                    this.x = cx + Math.cos(this.angle) * (majorR + Math.cos(this.poloidalAngle) * currentMinorR * 0.4);
                    this.y = cy + Math.sin(this.angle) * (minorR * 0.55 + Math.sin(this.poloidalAngle) * currentMinorR * 0.3);
                }
            }

            // Inicializar partículas
            function initParticles(cx, cy, majorR, minorR) {
                simState.particles = [];
                for (let i = 0; i < 45; i++) simState.particles.push(new PlasmaParticle('deut', cx, cy, majorR, minorR));
                for (let i = 0; i < 45; i++) simState.particles.push(new PlasmaParticle('trit', cx, cy, majorR, minorR));
            }

            // ENGINE FÍSICA E CÁLCULO DAS REAÇÕES TERMONUCLEARES
            function updatePhysics() {
                if (simState.isDisrupted) return;

                // 1. Injeção de Combustível afeta Densidade
                const targetDensity = simState.fuelPelletsPerSec / 50.0;
                simState.density += (targetDensity - simState.density) * 0.05;

                // 2. Seção de Choque do Reação D-T: <sigma v>
                // A fusão D-T atinge o pico de reatividade perto de 150-200 Milhões °C
                const T = simState.plasmaTempMdegC;
                let reactivity = 0;
                if (T > 8.0) {
                    // Aproximação gaussiana da curva de reatividade D-T
                    reactivity = Math.exp(-Math.pow((T - 160) / 75, 2));
                }

                // 3. Potência de Fusão D-T (P_fus ~ n^2 * <sigma v>)
                simState.fusionPowerMW = 2400 * Math.pow(simState.density, 2) * reactivity;

                // Partículas Alfa (20% da fusão) reaquecem o plasma
                const alphaHeatingMW = simState.fusionPowerMW * 0.20;
                // Nêutrons (80% da fusão) saem para o Manto de Lítio
                const neutronPowerMW = simState.fusionPowerMW * 0.80;

                // 4. Perdas Térmicas (Radiação Bremsstrahlung e Condução)
                // Maior B reduz perdas; Maior Temp e Densidade aumentam perdas
                const lossPowerMW = (0.35 * Math.pow(T, 1.35) * simState.density) / Math.pow(simState.magneticFieldT, 1.1);

                // Balanço Térmico
                const netHeatingMW = simState.auxHeatingMW + alphaHeatingMW - lossPowerMW;
                simState.plasmaTempMdegC += netHeatingMW * 0.004;

                // Temperatura mínima ambiental
                if (simState.plasmaTempMdegC < 2.5) simState.plasmaTempMdegC = 2.5;

                // 5. Fator de Ganho de Energia (Q = P_fusão / P_aquecimento_aux)
                if (simState.auxHeatingMW > 0.5) {
                    simState.qFactor = simState.fusionPowerMW / simState.auxHeatingMW;
                } else {
                    simState.qFactor = simState.fusionPowerMW > 5.0 ? 99.9 : 0.0;
                }

                // 6. Potência Elétrica Líquida
                // Conversão do calor do Blanket (45% eficácia) + recuperador - consumo dos supercondutores e aux
                const magnetCoolingPowerMW = Math.pow(simState.magneticFieldT, 1.6) * 1.8;
                simState.netPowerMW = (neutronPowerMW * 0.48) + (lossPowerMW * 0.15) - simState.auxHeatingMW - magnetCoolingPowerMW;

                // 7. Critério de Lawson (Produto Triplo: n · T · τE)
                // Confinamento do tempo τE melhora com B e piora com potência total
                const totalInputP = Math.max(1, simState.auxHeatingMW + alphaHeatingMW);
                const tauE = (0.22 * Math.pow(simState.magneticFieldT, 1.2) * simState.density) / Math.sqrt(totalInputP);
                simState.lawsonTripleProduct = simState.density * (simState.plasmaTempMdegC * 0.086) * tauE; // em 10^21 keV s m^-3

                // 8. Estabilidade do Campo Magnético & Pressão Beta (β)
                // Se a pressão térmica do plasma excede a pressão magnética (B^2 / 2µ0)
                const thermalPressure = simState.density * simState.plasmaTempMdegC;
                const magneticPressure = Math.pow(simState.magneticFieldT, 2) * 14.0;
                const betaRatio = thermalPressure / magneticPressure;

                if (betaRatio > 0.75) {
                    const instabilityRate = (betaRatio - 0.75) * 12.0;
                    simState.stabilityPct -= instabilityRate;
                } else {
                    simState.stabilityPct += (100.0 - simState.stabilityPct) * 0.08;
                }

                simState.stabilityPct = Math.max(0.0, Math.min(100.0, simState.stabilityPct));

                // Disrupção se a estabilidade zerar
                if (simState.stabilityPct <= 0 && !simState.isDisrupted) {
                    triggerDisruption();
                }

                // Temperatura do Manto de Lítio (Blanket)
                const targetBlanketTemp = 25.0 + (neutronPowerMW * 0.35);
                simState.blanketTempC += (targetBlanketTemp - simState.blanketTempC) * 0.03;

                // Spawn de Nêutrons se houver fusão ativa
                if (simState.fusionPowerMW > 10.0 && Math.random() < 0.4) {
                    const rect = canvas.getBoundingClientRect();
                    const cx = rect.width / 2;
                    const cy = rect.height / 2;
                    simState.particles.push(new PlasmaParticle('neutron', cx, cy, rect.width * 0.28, rect.height * 0.25));
                }

                // Spawn de Partículas Alfa na Fusão
                if (simState.fusionPowerMW > 50.0 && Math.random() < 0.2) {
                    const rect = canvas.getBoundingClientRect();
                    const cx = rect.width / 2;
                    const cy = rect.height / 2;
                    simState.particles.push(new PlasmaParticle('alpha', cx, cy, rect.width * 0.28, rect.height * 0.25));
                    if (simState.particles.length > 130) simState.particles.splice(0, 1);
                }
            }

            function triggerDisruption() {
                simState.isDisrupted = true;
                simState.plasmaTempMdegC = 2.5;
                simState.fusionPowerMW = 0;
                simState.netPowerMW = -simState.auxHeatingMW;
                simState.qFactor = 0;
                disruptionOverlay.classList.remove('hidden');
            }

            // RENDERIZAÇÃO GRÁFICA NO CANVAS 2D
            function renderCanvas() {
                const width = canvas.width / window.devicePixelRatio;
                const height = canvas.height / window.devicePixelRatio;

                ctx.clearRect(0, 0, width, height);

                const cx = width / 2;
                const cy = height / 2;
                const majorR = Math.min(width, height) * 0.30;
                const minorR = Math.min(width, height) * 0.18;

                simState.time += 0.02;

                // 1. DESENHAR ESTRUTURA DO VASO DE VÁCUO (CRYOSTAT & TOKAMAK SHELL)
                ctx.save();
                
                // Camada Externa de Vácuo
                ctx.beginPath();
                ctx.ellipse(cx, cy, majorR + minorR * 1.5, (majorR + minorR * 1.5) * 0.55, 0, 0, Math.PI * 2);
                ctx.fillStyle = '#060d1e';
                ctx.fill();
                ctx.strokeStyle = 'rgba(0, 240, 255, 0.2)';
                ctx.lineWidth = 2;
                ctx.stroke();

                // 2. MANTO REABSORVEDOR (BLANKET DE LÍTIO)
                // Brilha em função da temperatura do manto absorvendo nêutrons
                const blanketGlow = Math.min(1.0, (simState.blanketTempC - 25.0) / 400.0);
                ctx.beginPath();
                ctx.ellipse(cx, cy, majorR + minorR * 1.15, (majorR + minorR * 1.15) * 0.55, 0, 0, Math.PI * 2);
                
                const blanketGrad = ctx.createRadialGradient(cx, cy, majorR * 0.5, cx, cy, majorR + minorR * 1.2);
                blanketGrad.addColorStop(0, 'rgba(15, 23, 42, 0.9)');
                blanketGrad.addColorStop(0.85, `rgba(${Math.floor(20 + blanketGlow * 200)}, ${Math.floor(40 + blanketGlow * 80)}, 50, 0.8)`);
                blanketGrad.addColorStop(1, 'rgba(30, 41, 59, 1)');
                
                ctx.fillStyle = blanketGrad;
                ctx.fill();
                ctx.strokeStyle = `rgba(${Math.floor(0 + blanketGlow * 255)}, 240, 255, 0.6)`;
                ctx.lineWidth = 3;
                ctx.stroke();

                // 3. BOBINAS MAGNÉTICAS TOROIDAIS (D-SHAPED SUPERCONDUCTING COILS)
                const coilCount = 14;
                for (let i = 0; i < coilCount; i++) {
                    const angle = (i / coilCount) * Math.PI * 2 + simState.time * 0.1;
                    const coilX = cx + Math.cos(angle) * (majorR + 5);
                    const coilY = cy + Math.sin(angle) * (minorR * 0.55 + 5);

                    ctx.save();
                    ctx.translate(coilX, coilY);
                    ctx.beginPath();
                    ctx.ellipse(0, 0, 16, 32, angle, 0, Math.PI * 2);
                    ctx.strokeStyle = 'rgba(0, 240, 255, 0.7)';
                    ctx.shadowColor = '#00f0ff';
                    ctx.shadowBlur = simState.magneticFieldT * 1.2;
                    ctx.lineWidth = 2.5;
                    ctx.stroke();
                    ctx.restore();
                }

                // 4. LINHAS DE CAMPO MAGNÉTICO HELICOIDAL
                if (!simState.isDisrupted) {
                    ctx.save();
                    ctx.setLineDash([6, 10]);
                    simState.magneticFieldOffset -= 0.8;
                    ctx.lineDashOffset = simState.magneticFieldOffset;

                    for (let j = -2; j <= 2; j++) {
                        ctx.beginPath();
                        ctx.ellipse(cx, cy, majorR + j * 8, (minorR + j * 4) * 0.55, 0, 0, Math.PI * 2);
                        ctx.strokeStyle = `rgba(0, 240, 255, ${0.15 + (simState.magneticFieldT / 15) * 0.25})`;
                        ctx.lineWidth = 1.2;
                        ctx.stroke();
                    }
                    ctx.restore();
                }

                // 5. ANEL DE PLASMA TOROIDAL SUPERAQUECIDO
                if (!simState.isDisrupted) {
                    ctx.save();

                    // Determinar cor do plasma com base na temperatura (Vermelho -> Laranja -> Amarelo -> Cyan/Violeta -> Branco)
                    const T = simState.plasmaTempMdegC;
                    let innerColor, outerColor, glowColor;

                    if (T < 20.0) {
                        innerColor = '#ff3300'; outerColor = 'rgba(180, 20, 0, 0.4)'; glowColor = '#ff2200';
                    } else if (T < 60.0) {
                        innerColor = '#ffaa00'; outerColor = 'rgba(230, 100, 0, 0.5)'; glowColor = '#ff9900';
                    } else if (T < 120.0) {
                        innerColor = '#00f0ff'; outerColor = 'rgba(0, 150, 255, 0.6)'; glowColor = '#00f0ff';
                    } else {
                        innerColor = '#ffffff'; outerColor = 'rgba(200, 100, 255, 0.7)'; glowColor = '#e0a0ff';
                    }

                    // Efeito de vibração/flutuação do plasma proporcional à instabilidade
                    const instabilityJitter = (100.0 - simState.stabilityPct) * 0.15;
                    const jitterX = (Math.random() - 0.5) * instabilityJitter;
                    const jitterY = (Math.random() - 0.5) * instabilityJitter;

                    // Brilho Neon Externo do Plasma
                    ctx.beginPath();
                    ctx.ellipse(cx + jitterX, cy + jitterY, majorR, minorR * 0.55, 0, 0, Math.PI * 2);
                    ctx.fillStyle = outerColor;
                    ctx.shadowColor = glowColor;
                    ctx.shadowBlur = 25 + (T / 10.0);
                    ctx.fill();

                    // Núcleo Incandescente do Plasma
                    ctx.beginPath();
                    ctx.ellipse(cx + jitterX, cy + jitterY, majorR * 0.92, minorR * 0.42, 0, 0, Math.PI * 2);
                    const coreGrad = ctx.createRadialGradient(cx, cy, 10, cx, cy, majorR);
                    coreGrad.addColorStop(0, '#ffffff');
                    coreGrad.addColorStop(0.3, innerColor);
                    coreGrad.addColorStop(1, 'transparent');
                    ctx.fillStyle = coreGrad;
                    ctx.fill();

                    ctx.restore();
                }

                // 6. RENDERIZAR E ATUALIZAR PARTÍCULAS NO PLASMA
                const tempRatio = Math.min(2.0, simState.plasmaTempMdegC / 100.0);

                for (let i = simState.particles.length - 1; i >= 0; i--) {
                    const p = simState.particles[i];
                    p.update(cx, cy, majorR, minorR, tempRatio);

                    // Desenhar partículas
                    ctx.save();
                    ctx.beginPath();

                    if (p.type === 'deut') {
                        ctx.arc(p.x, p.y, 2.5, 0, Math.PI * 2);
                        ctx.fillStyle = '#00f0ff';
                        ctx.shadowColor = '#00f0ff';
                        ctx.shadowBlur = 6;
                    } else if (p.type === 'trit') {
                        ctx.arc(p.x, p.y, 2.8, 0, Math.PI * 2);
                        ctx.fillStyle = '#00ff66';
                        ctx.shadowColor = '#00ff66';
                        ctx.shadowBlur = 6;
                    } else if (p.type === 'alpha') {
                        ctx.arc(p.x, p.y, 4.0, 0, Math.PI * 2);
                        ctx.fillStyle = '#ffaa00';
                        ctx.shadowColor = '#ffaa00';
                        ctx.shadowBlur = 10;
                    } else if (p.type === 'neutron') {
                        ctx.arc(p.x, p.y, 1.8, 0, Math.PI * 2);
                        ctx.fillStyle = '#ffffff';
                        ctx.shadowColor = '#ffffff';
                        ctx.shadowBlur = 8;

                        // Rastro do Nêutron
                        ctx.moveTo(p.x, p.y);
                        ctx.lineTo(p.x - p.vx * 3, p.y - p.vy * 3);
                        ctx.strokeStyle = 'rgba(255, 255, 255, 0.6)';
                        ctx.stroke();

                        // Remover nêutrons que saíram do reator ou expiraram
                        if (p.life <= 0 || p.x < 0 || p.x > width || p.y < 0 || p.y > height) {
                            simState.particles.splice(i, 1);
                            continue;
                        }
                    }

                    ctx.fill();
                    ctx.restore();
                }

                // Efeito visual de Faíscas de Disrupção quando a estabilidade estiver baixa
                if (simState.stabilityPct < 40.0 && !simState.isDisrupted) {
                    for (let s = 0; s < 3; s++) {
                        const sparkX = cx + (Math.random() - 0.5) * (majorR * 2);
                        const sparkY = cy + (Math.random() - 0.5) * (minorR * 2);
                        ctx.fillStyle = '#ff2a2a';
                        ctx.fillRect(sparkX, sparkY, 2, 2);
                    }
                }

                ctx.restore();
            }

            // ATUALIZAÇÃO DA INTERFACE DE USUÁRIO (UI TELEMETRIA)
            function updateUI() {
                // Métricas Básicas
                metricTemp.innerText = simState.plasmaTempMdegC.toFixed(1);
                
                // Formatação da Potência Líquida (MW ou GW)
                if (Math.abs(simState.netPowerMW) >= 1000) {
                    metricNetPower.innerText = (simState.netPowerMW / 1000).toFixed(2);
                    metricNetPowerUnit.innerText = 'GW';
                } else {
                    metricNetPower.innerText = Math.round(simState.netPowerMW);
                    metricNetPowerUnit.innerText = 'MW';
                }

                metricFusionPower.innerText = simState.fusionPowerMW.toFixed(1);
                metricStability.innerText = Math.round(simState.stabilityPct);

                // Estilização do Indicador de Estabilidade
                if (simState.stabilityPct < 30) {
                    metricStability.className = 'font-orbitron font-bold text-base text-rose-500 animate-pulse';
                } else if (simState.stabilityPct < 70) {
                    metricStability.className = 'font-orbitron font-bold text-base text-amber-400';
                } else {
                    metricStability.className = 'font-orbitron font-bold text-base text-emerald-400';
                }

                // Fator Q
                if (simState.qFactor >= 99.9) {
                    qFactorVal.innerText = 'IGNIÇÃO (∞)';
                    qFactorVal.className = 'font-orbitron font-bold text-lg text-purple-300 glow-magenta';
                } else {
                    qFactorVal.innerText = simState.qFactor.toFixed(2);
                    if (simState.qFactor >= 20.0) {
                        qFactorVal.className = 'font-orbitron font-bold text-lg text-cyan-300 glow-cyan';
                    } else if (simState.qFactor >= 1.0) {
                        qFactorVal.className = 'font-orbitron font-bold text-lg text-amber-400 glow-amber';
                    } else {
                        qFactorVal.className = 'font-orbitron font-bold text-lg text-rose-400';
                    }
                }

                // Produto Triplo de Lawson
                lawsonVal.innerText = `${simState.lawsonTripleProduct.toFixed(2)} / 3.00`;
                const lawsonPct = Math.min(100, (simState.lawsonTripleProduct / 3.00) * 100);
                lawsonBar.style.width = `${lawsonPct}%`;

                if (lawsonPct >= 100) {
                    lawsonDesc.innerText = 'Ignição autossustentada atingida! Plasma autoaquecido por partículas Alfa.';
                    lawsonDesc.className = 'text-[10px] text-cyan-300 font-mono-tech font-bold';
                } else if (lawsonPct >= 33) {
                    lawsonDesc.innerText = 'Regime de Break-even comercial (Q > 1). Fusão produz mais energia do que consome.';
                    lawsonDesc.className = 'text-[10px] text-amber-300 font-mono-tech';
                } else {
                    lawsonDesc.innerText = 'Abaixo do limiar de ignição. Aumente temperatura e confinamento.';
                    lawsonDesc.className = 'text-[10px] text-slate-400 font-mono-tech';
                }

                // Status Badge Geral
                if (simState.isDisrupted) {
                    statusBadge.className = 'flex items-center gap-2 px-3 py-1.5 rounded-full bg-red-950/80 border border-red-500/50 text-red-400 font-orbitron text-xs';
                    statusText.innerText = 'DISRUPÇÃO DE PLASMA';
                } else if (simState.qFactor >= 20.0) {
                    statusBadge.className = 'flex items-center gap-2 px-3 py-1.5 rounded-full bg-cyan-950/80 border border-cyan-400/60 text-cyan-300 font-orbitron text-xs glow-cyan';
                    statusText.innerText = 'IGNIÇÃO COMERCIAL ATIVA';
                } else if (simState.qFactor >= 1.0) {
                    statusBadge.className = 'flex items-center gap-2 px-3 py-1.5 rounded-full bg-amber-950/80 border border-amber-500/50 text-amber-300 font-orbitron text-xs';
                    statusText.innerText = 'PONTO DE BREAK-EVEN (Q≥1)';
                } else {
                    statusBadge.className = 'flex items-center gap-2 px-3 py-1.5 rounded-full bg-emerald-950/60 border border-emerald-500/40 text-emerald-400 font-orbitron text-xs';
                    statusText.innerText = 'AQUECIMENTO AUXILIAR';
                }

                // HUD Canvas
                hudBField.innerText = `${simState.magneticFieldT.toFixed(1)} T`;
                hudDensity.innerText = `${simState.density.toFixed(2)} ×10²⁰ m⁻³`;

                // Rede Elétrica da Cidade
                if (simState.netPowerMW > 0) {
                    const homes = Math.floor(simState.netPowerMW * 1000); // ~1kW por residência
                    cityHousesVal.innerText = `${homes.toLocaleString('pt-BR')} Residências Alimentadas`;
                    cityHousesVal.className = 'font-orbitron font-bold text-sm text-emerald-400 glow-cyan';
                } else {
                    cityHousesVal.innerText = '0 Residências (Consumindo da Rede Externa)';
                    cityHousesVal.className = 'font-orbitron font-bold text-sm text-rose-400';
                }

                // Manto Térmico (Blanket)
                const blanketPct = Math.min(100, (simState.blanketTempC / 600.0) * 100);
                blanketHeatBar.style.width = `${blanketPct}%`;
                blanketTempText.innerText = `${Math.round(simState.blanketTempC)}°C`;
            }

            // EVENT LISTENERS & CONTROLES DOS SLIDERS
            inputAuxPower.addEventListener('input', (e) => {
                simState.auxHeatingMW = parseFloat(e.target.value);
                valAuxPower.innerText = `${simState.auxHeatingMW} MW`;
            });

            inputBField.addEventListener('input', (e) => {
                simState.magneticFieldT = parseFloat(e.target.value);
                valBField.innerText = `${simState.magneticFieldT.toFixed(1)} Tesla`;
            });

            inputFuelRate.addEventListener('input', (e) => {
                simState.fuelPelletsPerSec = parseInt(e.target.value);
                valFuelRate.innerText = `${simState.fuelPelletsPerSec} Pellets/s`;
            });

            // Botão de Pânico / Purga de Emergência
            btnPanic.addEventListener('click', () => {
                simState.plasmaTempMdegC = 2.5;
                simState.fuelPelletsPerSec = 5;
                inputFuelRate.value = 5;
                valFuelRate.innerText = '5 Pellets/s';
                simState.auxHeatingMW = 0;
                inputAuxPower.value = 0;
                valAuxPower.innerText = '0 MW';
                simState.stabilityPct = 100.0;
                simState.isDisrupted = false;
                disruptionOverlay.classList.add('hidden');
            });

            // Botão Reset Pós-Disrupção
            btnResetDisruption.addEventListener('click', () => {
                simState.isDisrupted = false;
                simState.stabilityPct = 100.0;
                simState.plasmaTempMdegC = 5.0;
                disruptionOverlay.classList.add('hidden');
            });

            // PRESETS DE OPERAÇÃO
            function applyPreset(aux, b, fuel) {
                if (simState.isDisrupted) {
                    simState.isDisrupted = false;
                    disruptionOverlay.classList.add('hidden');
                }
                simState.auxHeatingMW = aux;
                inputAuxPower.value = aux;
                valAuxPower.innerText = `${aux} MW`;

                simState.magneticFieldT = b;
                inputBField.value = b;
                valBField.innerText = `${b.toFixed(1)} Tesla`;

                simState.fuelPelletsPerSec = fuel;
                inputFuelRate.value = fuel;
                valFuelRate.innerText = `${fuel} Pellets/s`;

                simState.stabilityPct = 100.0;
            }

            presetStandby.addEventListener('click', () => applyPreset(5, 5.0, 10));
            presetBreakeven.addEventListener('click', () => applyPreset(40, 8.5, 45));
            presetIgnition.addEventListener('click', () => applyPreset(20, 13.5, 85));
            presetStress.addEventListener('click', () => applyPreset(95, 3.2, 95));

            // LOOP DE ANIMAÇÃO A 60 FPS
            function loop() {
                updatePhysics();
                renderCanvas();
                updateUI();
                requestAnimationFrame(loop);
            }

            // Inicialização no carregamento da janela
            window.onload = function() {
                const rect = canvas.getBoundingClientRect();
                initParticles(rect.width / 2, rect.height / 2, rect.width * 0.28, rect.height * 0.25);
                loop();
            };
        })();
    </script>
</body>
</html>
