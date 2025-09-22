<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>OxCal Model Generator</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .tooltip {
            position: relative;
            display: inline-block;
        }
        .tooltip .tooltiptext {
            visibility: hidden;
            width: 220px;
            background-color: #555;
            color: #fff;
            text-align: center;
            border-radius: 6px;
            padding: 5px;
            position: absolute;
            z-index: 1;
            bottom: 125%;
            left: 50%;
            margin-left: -110px;
            opacity: 0;
            transition: opacity 0.3s;
        }
        .tooltip:hover .tooltiptext {
            visibility: visible;
            opacity: 1;
        }
    </style>
</head>
<body class="bg-gray-100 dark:bg-gray-900 text-gray-800 dark:text-gray-200 min-h-screen flex items-center justify-center p-4">

    <div class="w-full max-w-4xl mx-auto space-y-6">
        <!-- Header -->
        <div class="text-center">
            <h1 class="text-4xl font-bold text-blue-600 dark:text-blue-400">OxCal Model Generator</h1>
            <p class="text-gray-600 dark:text-gray-400 mt-2">Create Bayesian chronological models with ease.</p>
        </div>

        <!-- Main Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
            
            <!-- Left Column: Inputs -->
            <div class="space-y-6">
                <!-- Global Options Card -->
                <div class="bg-white dark:bg-gray-800 p-6 rounded-xl shadow-md">
                    <h2 class="text-2xl font-semibold border-b border-gray-200 dark:border-gray-700 pb-3 mb-4">Global Options</h2>
                    <div class="space-y-4">
                        <div>
                            <label for="curve" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Calibration Curve</label>
                            <select id="curve" class="mt-1 block w-full bg-gray-50 dark:bg-gray-700 border-gray-300 dark:border-gray-600 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500">
                                <option>IntCal20</option>
                                <option>SHCal20</option>
                                <option>Marine20</option>
                            </select>
                        </div>
                        <div class="flex items-center">
                            <input id="use-outlier" type="checkbox" checked class="h-4 w-4 text-blue-600 border-gray-300 dark:border-gray-600 rounded focus:ring-blue-500">
                            <label for="use-outlier" class="ml-2 block text-sm text-gray-900 dark:text-gray-300">
                                Use Outlier Model
                                <span class="tooltip text-gray-500 dark:text-gray-400">(?)
                                    <span class="tooltiptext">Recommended for most models to handle potential outlier dates statistically.</span>
                                </span>
                            </label>
                        </div>
                    </div>
                </div>

                <!-- Layers Card -->
                <div class="bg-white dark:bg-gray-800 p-6 rounded-xl shadow-md">
                    <h2 class="text-2xl font-semibold border-b border-gray-200 dark:border-gray-700 pb-3 mb-4">Chronological Layers</h2>
                    <div id="layers-container" class="space-y-4">
                        <!-- Dynamic layers will be injected here -->
                    </div>
                    <button id="add-layer-btn" class="mt-4 w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-md transition duration-300">
                        + Add Layer
                    </button>
                </div>
            </div>

            <!-- Right Column: Output & Tips -->
            <div class="space-y-6">
                <!-- Output Card -->
                <div class="bg-white dark:bg-gray-800 p-6 rounded-xl shadow-md h-full flex flex-col">
                    <h2 class="text-2xl font-semibold border-b border-gray-200 dark:border-gray-700 pb-3 mb-4">Generated CQL2 Model</h2>
                    <div class="relative flex-grow">
                        <pre id="output-code" class="w-full h-full bg-gray-100 dark:bg-gray-900 text-sm p-4 rounded-md overflow-x-auto min-h-[200px] whitespace-pre-wrap font-mono"></pre>
                        <button id="copy-btn" class="absolute top-2 right-2 bg-gray-200 dark:bg-gray-700 hover:bg-gray-300 dark:hover:bg-gray-600 text-xs font-semibold py-1 px-3 rounded-md transition duration-300">Copy</button>
                    </div>
                     <button id="generate-btn" class="mt-4 w-full bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-md transition duration-300">
                        Generate Model
                    </button>
                </div>

                <!-- Tips Card -->
                <div class="bg-white dark:bg-gray-800 p-6 rounded-xl shadow-md">
                    <h2 class="text-xl font-semibold mb-3">Tips</h2>
                    <ul class="list-disc list-inside space-y-2 text-sm text-gray-600 dark:text-gray-400">
                        <li>For marine samples, use `Marine20` and consider adding a local reservoir correction (`Delta_R`).</li>
                        <li>Add multiple `R_Date` lines inside a layer for multiple dates.</li>
                        <li>After running in OxCal, check the `A_model` / `A_overall` values. Aim for ≥60% as a general guideline.</li>
                        <li>Layers are ordered from youngest (top) to oldest (bottom) in the model.</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // State
            let layers = [];

            // DOM Elements
            const layersContainer = document.getElementById('layers-container');
            const addLayerBtn = document.getElementById('add-layer-btn');
            const generateBtn = document.getElementById('generate-btn');
            const copyBtn = document.getElementById('copy-btn');
            const outputCode = document.getElementById('output-code');
            const curveSelect = document.getElementById('curve');
            const useOutlierCheck = document.getElementById('use-outlier');

            // --- Core Functions ---

            const renderLayers = () => {
                layersContainer.innerHTML = '';
                if (layers.length === 0) {
                    layersContainer.innerHTML = `<p class="text-center text-sm text-gray-500 dark:text-gray-400">Add a layer to begin.</p>`;
                }
                layers.forEach((layer, index) => {
                    const layerEl = document.createElement('div');
                    layerEl.className = 'p-4 border border-gray-200 dark:border-gray-700 rounded-lg relative';
                    layerEl.innerHTML = `
                        <button class="remove-layer-btn absolute -top-2 -right-2 bg-red-500 hover:bg-red-600 text-white font-bold h-6 w-6 rounded-full text-xs" data-index="${index}">&times;</button>
                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-sm font-medium">Phase Name</label>
                                <input type="text" placeholder="e.g., Upper Layer" value="${layer.phase}" data-index="${index}" data-field="phase" class="layer-input mt-1 w-full bg-gray-50 dark:bg-gray-700 border-gray-300 dark:border-gray-600 rounded-md shadow-sm text-sm">
                            </div>
                            <div>
                                <label class="block text-sm font-medium">Sample ID</label>
                                <input type="text" placeholder="e.g., Bone_Sample_1" value="${layer.id}" data-index="${index}" data-field="id" class="layer-input mt-1 w-full bg-gray-50 dark:bg-gray-700 border-gray-300 dark:border-gray-600 rounded-md shadow-sm text-sm">
                            </div>
                            <div>
                                <label class="block text-sm font-medium">BP Age</label>
                                <input type="number" placeholder="2850" value="${layer.bp}" data-index="${index}" data-field="bp" class="layer-input mt-1 w-full bg-gray-50 dark:bg-gray-700 border-gray-300 dark:border-gray-600 rounded-md shadow-sm text-sm">
                            </div>
                            <div>
                                <label class="block text-sm font-medium">Error (σ)</label>
                                <input type="number" placeholder="30" value="${layer.error}" data-index="${index}" data-field="error" class="layer-input mt-1 w-full bg-gray-50 dark:bg-gray-700 border-gray-300 dark:border-gray-600 rounded-md shadow-sm text-sm">
                            </div>
                        </div>
                    `;
                    layersContainer.appendChild(layerEl);
                });
            };

            const generateCode = () => {
                const curve = curveSelect.value;
                const useOutlier = useOutlierCheck.checked;
                let code = `// Model Generated on ${new Date().toLocaleDateString()}\n`;
                code += `Options(){\n`;
                code += `  Curve("${curve}");\n`;
                code += `  Resolution(1);\n`;
                if (useOutlier) {
                    code += `  Outlier_Model("General", T(5), U(0,0.05));\n`;
                }
                code += `};\n\n`;

                code += `Sequence("My Model"){\n`;
                
                // OxCal sequences are defined stratigraphically, so we proceed through the array
                if (layers.length > 0) {
                     code += `  Boundary("Start");\n`;
                }

                layers.forEach((layer, index) => {
                    const phaseName = layer.phase || `Layer ${index + 1}`;
                    const sampleId = layer.id || `Sample_${index + 1}`;
                    const bp = layer.bp || 0;
                    const error = layer.error || 0;
                    
                    code += `  Phase("${phaseName}"){\n`;
                    code += `    R_Date("${sampleId}", ${bp}, ${error})`;
                    if (useOutlier) {
                        code += `{ Outlier("General",0.05); }`;
                    }
                    code += `;\n`;
                    code += `  };\n`;
                    
                    if (index < layers.length - 1) {
                        code += `  Boundary("Boundary ${index + 1}");\n`;
                    }
                });

                 if (layers.length > 0) {
                     code += `  Boundary("End");\n`;
                }

                code += `};\n`;

                outputCode.textContent = code;
            };

            const addLayer = (phase = '', id = '', bp = '', error = '') => {
                layers.push({ phase, id, bp, error });
                renderLayers();
            };

            const removeLayer = (index) => {
                layers.splice(index, 1);
                renderLayers();
            };

            // --- Event Listeners ---

            addLayerBtn.addEventListener('click', () => addLayer());

            layersContainer.addEventListener('click', (e) => {
                if (e.target.classList.contains('remove-layer-btn')) {
                    const index = parseInt(e.target.dataset.index, 10);
                    removeLayer(index);
                }
            });

            layersContainer.addEventListener('input', (e) => {
                if (e.target.classList.contains('layer-input')) {
                    const index = parseInt(e.target.dataset.index, 10);
                    const field = e.target.dataset.field;
                    layers[index][field] = e.target.value;
                }
            });

            generateBtn.addEventListener('click', generateCode);
            
            copyBtn.addEventListener('click', () => {
                const textToCopy = outputCode.textContent;
                // Using document.execCommand for better compatibility in sandboxed environments
                const textArea = document.createElement('textarea');
                textArea.value = textToCopy;
                document.body.appendChild(textArea);
                textArea.select();
                try {
                    document.execCommand('copy');
                    copyBtn.textContent = 'Copied!';
                    setTimeout(() => { copyBtn.textContent = 'Copy'; }, 2000);
                } catch (err) {
                    console.error('Failed to copy text: ', err);
                    copyBtn.textContent = 'Failed!';
                    setTimeout(() => { copyBtn.textContent = 'Copy'; }, 2000);
                }
                document.body.removeChild(textArea);
            });

            // --- Initialisation ---
            
            // Add initial layers based on the example
            addLayer('Upper Layer', 'Bone_Sample_1', '2850', '30');
            addLayer('Lower Layer', 'Charcoal_Sample_2', '2950', '35');

            // Generate initial code on load
            generateCode(); 
        });
    </script>
</body>
</html>
