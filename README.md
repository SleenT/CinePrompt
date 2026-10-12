# CinePrompt
A professional cinematography library and AI prompt builder for high-end video generation. Master camera framing, lenses, and lighting.
<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CinePrompt Pro | AI Cinematography Library</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        mac: {
                            bg: '#f5f5f7',
                            sidebar: 'rgba(232, 232, 237, 0.8)',
                            accent: '#0071e3',
                            card: '#ffffff',
                            darkBg: '#121212',
                            darkSidebar: 'rgba(28, 28, 30, 0.9)',
                            darkCard: '#1c1c1e'
                        }
                    }
                }
            }
        }
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            transition: background-color 0.3s ease, color 0.3s ease;
        }

        .sidebar-glass {
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
        }

        .mac-card {
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .mac-card:hover {
            transform: translateY(-2px);
        }

        .selected-ring {
            box-shadow: 0 0 0 2px #0071e3;
        }

        .dark .selected-ring {
            box-shadow: 0 0 0 2px #0a84ff;
        }

        .ai-gradient {
            background: linear-gradient(135deg, #0071e3, #8e2de2);
        }

        #modal-overlay {
            background-color: rgba(0, 0, 0, 0.4);
            backdrop-filter: blur(4px);
        }

        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
    </style>
</head>
<body class="h-screen flex bg-mac-bg dark:bg-mac-darkBg text-gray-900 dark:text-gray-100 overflow-hidden">

    <!-- Modal for Adding Items -->
    <div id="modal-overlay" class="fixed inset-0 z-50 flex items-center justify-center hidden opacity-0 transition-opacity duration-300">
        <div class="bg-white dark:bg-mac-darkCard w-full max-w-md p-8 rounded-3xl shadow-2xl transform scale-95 transition-transform duration-300">
            <h2 class="text-2xl font-bold mb-6">Add New Entry</h2>
            <form id="add-item-form" class="space-y-4">
                <div>
                    <label class="block text-[11px] font-bold text-gray-400 uppercase tracking-widest mb-2">Category</label>
                    <input type="text" id="new-category" list="category-list" placeholder="Select or type new..." class="w-full p-3 rounded-xl bg-gray-50 dark:bg-black/20 border border-gray-100 dark:border-gray-800 outline-none focus:ring-2 focus:ring-blue-500 text-sm">
                    <datalist id="category-list"></datalist>
                </div>
                <div>
                    <label class="block text-[11px] font-bold text-gray-400 uppercase tracking-widest mb-2">Asset Name</label>
                    <input type="text" id="new-name" placeholder="e.g. Low Angle" class="w-full p-3 rounded-xl bg-gray-50 dark:bg-black/20 border border-gray-100 dark:border-gray-800 outline-none focus:ring-2 focus:ring-blue-500 text-sm">
                </div>
                <div>
                    <label class="block text-[11px] font-bold text-gray-400 uppercase tracking-widest mb-2">Keyword</label>
                    <input type="text" id="new-keyword" placeholder="e.g. low angle shot" class="w-full p-3 rounded-xl bg-gray-50 dark:bg-black/20 border border-gray-100 dark:border-gray-800 outline-none focus:ring-2 focus:ring-blue-500 text-sm">
                </div>
                <div>
                    <label class="block text-[11px] font-bold text-gray-400 uppercase tracking-widest mb-2">Description</label>
                    <textarea id="new-desc" placeholder="Brief explanation..." class="w-full p-3 rounded-xl bg-gray-50 dark:bg-black/20 border border-gray-100 dark:border-gray-800 outline-none focus:ring-2 focus:ring-blue-500 text-sm h-20 resize-none"></textarea>
                </div>
                <div class="flex gap-3 pt-4">
                    <button type="button" onclick="closeModal()" class="flex-1 py-3 bg-gray-100 dark:bg-white/5 rounded-xl font-bold text-sm">Cancel</button>
                    <button type="submit" class="flex-1 py-3 ai-gradient text-white rounded-xl font-bold text-sm">Save Entry</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Sidebar -->
    <aside class="sidebar-glass w-64 flex-shrink-0 flex flex-col h-full border-r border-gray-200 dark:border-gray-800 bg-mac-sidebar dark:bg-mac-darkSidebar z-30">
        <div class="p-6 flex items-center gap-3">
            <div class="w-8 h-8 ai-gradient rounded-lg flex items-center justify-center text-white shadow-lg">
                <i class="fas fa-video text-sm"></i>
            </div>
            <span class="font-bold text-lg tracking-tight">CinePrompt</span>
        </div>

        <nav class="flex-grow px-3 space-y-1 overflow-y-auto no-scrollbar">
            <button onclick="setView('ai')" id="nav-ai" class="w-full flex items-center gap-3 px-3 py-3 rounded-lg text-sm font-semibold transition-all hover:bg-black/5 dark:hover:bg-white/5 group">
                <i class="fas fa-robot text-blue-500 group-hover:scale-110 transition-transform"></i>
                AI Prompt Builder
            </button>

            <div class="mt-6 mb-2 px-3 text-[11px] font-bold text-gray-400 uppercase tracking-widest">Library</div>
            
            <button onclick="setView('all')" id="nav-all" class="w-full flex items-center gap-3 px-3 py-2 rounded-lg text-sm font-medium transition-all hover:bg-black/5 dark:hover:bg-white/5">
                <i class="fas fa-layer-group w-4 text-gray-400"></i> All Assets
            </button>
            
            <div id="category-nav" class="space-y-1">
                <!-- JS Populated -->
            </div>
        </nav>

        <div class="mt-auto border-t border-gray-200 dark:border-gray-800">
            <!-- Contextual Tip -->
            <div class="p-4 pb-0">
                <p id="context-tip" class="text-[10px] text-gray-400 leading-relaxed font-medium transition-opacity duration-300 whitespace-pre-line"></p>
            </div>
            
            <div class="p-4">
                <button onclick="toggleTheme()" class="w-full flex items-center justify-between px-3 py-2 rounded-lg bg-gray-200/50 dark:bg-white/5 text-sm font-medium transition-colors">
                    <span id="theme-text">Light Mode</span>
                    <i id="theme-icon" class="fas fa-sun"></i>
                </button>
            </div>
        </div>
    </aside>

    <!-- Main Content -->
    <main class="flex-grow flex flex-col relative overflow-hidden">
        <header class="h-16 flex-shrink-0 border-b border-gray-200 dark:border-gray-800 bg-white/50 dark:bg-mac-darkCard/50 backdrop-blur-md flex items-center justify-between px-8 z-20">
            <div class="flex items-center flex-1 max-w-xl bg-gray-200/50 dark:bg-white/5 rounded-full px-4 py-1.5 border border-transparent focus-within:border-blue-500 transition-all">
                <i class="fas fa-search text-gray-400 text-sm mr-3"></i>
                <input type="text" id="search-input" placeholder="Search optics, angles, or movements..." 
                       class="bg-transparent border-none outline-none w-full text-sm font-medium placeholder-gray-400" onkeyup="handleSearch()">
            </div>
            
            <div class="flex items-center gap-3 ml-4">
                <div id="selection-counter" class="hidden items-center gap-2 px-3 py-1 bg-blue-500 rounded-full text-[10px] font-bold text-white uppercase tracking-tighter">
                    <span id="count-num">0</span> Selected
                </div>
                <button onclick="openModal()" class="w-8 h-8 rounded-full bg-blue-500 hover:bg-blue-600 text-white flex items-center justify-center transition-transform hover:scale-110 shadow-lg">
                    <i class="fas fa-plus text-xs"></i>
                </button>
            </div>
        </header>

        <div id="main-scroll" class="flex-grow overflow-y-auto p-8 no-scrollbar">
            <!-- JS Populated Content -->
        </div>
    </main>

    <div id="toast" class="fixed bottom-8 left-1/2 -translate-x-1/2 px-6 py-3 bg-gray-900 text-white text-sm font-medium rounded-full shadow-2xl opacity-0 pointer-events-none transition-all duration-300 z-50">
        Prompt copied!
    </div>

    <script>
        let libraryData = [
            // 1. Framing & Shot Type
            { id: 'fra_1', category: 'Framing & Shot Type', name: 'Wide Shot', keyword: "wide shot", description: 'Shows the subject and the immediate environment.', example: 'Wide shot of a dancer on an empty stage.' },
            { id: 'fra_1_2', category: 'Framing & Shot Type', name: 'Extreme Wide', keyword: "extreme wide shot", description: 'Shows environment scale, subject is tiny.', example: 'Extreme wide shot of a lone hiker on a glacier.' },
            { id: 'fra_2', category: 'Framing & Shot Type', name: 'Establishing Shot', keyword: "establishing shot", description: 'Shows the location before the action.', example: 'An establishing shot of a futuristic Tokyo at night.' },
            { id: 'fra_3', category: 'Framing & Shot Type', name: 'Medium Shot', keyword: "medium shot", description: 'Waist-up framing, showing subject and context.', example: 'Medium shot of a barista making latte art.' },
            { id: 'fra_4', category: 'Framing & Shot Type', name: 'Close-up', keyword: "close-up shot", description: 'Focuses on the subject\'s head or face.', example: 'Close-up of a weathered sailor looking at the sea.' },
            { id: 'fra_4_2', category: 'Framing & Shot Type', name: 'Extreme Close-up (ECU)', keyword: "extreme close-up", description: 'Focus on fine details like an eye or a fingerprint.', example: 'Extreme close-up of a blue iris reflecting a sunset.' },
            { id: 'fra_5', category: 'Framing & Shot Type', name: 'Over-the-Shoulder', keyword: "over-the-shoulder shot", description: 'Camera looking past one person to another.', example: 'OTS shot of two rivals arguing.' },
            { id: 'fra_6', category: 'Framing & Shot Type', name: 'Two-Shot', keyword: "two-shot", description: 'Two characters framed together in the scene.', example: 'A two-shot of a couple sitting on a park bench.' },
            { id: 'fra_7', category: 'Framing & Shot Type', name: 'Locked-off Tripod', keyword: "locked-off tripod shot", description: 'Static, stable camera with zero movement.', example: '“Medium close-up, eye-level, locked-off camera”' },
            { id: 'fra_8', category: 'Framing & Shot Type', name: 'POV Shot', keyword: "POV shot", description: 'Through the eyes of a character.', example: 'First-person POV of a pilot in a cockpit.' },

            // 2. Camera Angles
            { id: 'ang_1', category: 'Camera Angles', name: 'Bird’s Eye View', keyword: "bird's eye view", description: 'Shot from directly above looking down.', example: 'A top-down bird\'s eye view of a city grid at night.' },
            { id: 'ang_2', category: 'Camera Angles', name: 'Worm’s Eye View', keyword: "worm's eye view", description: 'Looking straight up from ground level.', example: 'Worm\'s eye view of giant sequoias.' },
            { id: 'ang_3', category: 'Camera Angles', name: 'Low Angle', keyword: "low angle shot", description: 'Camera looks up at the subject to show power.', example: 'Low angle shot of a giant robot.' },
            { id: 'ang_3_2', category: 'Camera Angles', name: 'High Angle', keyword: "high angle shot", description: 'Camera looks down at the subject.', example: 'High angle shot of a lone traveler in a desert.' },
            { id: 'ang_4', category: 'Camera Angles', name: 'Dutch Angle', keyword: "dutch angle shot", description: 'Tilted horizon to create unease.', example: 'A character running through a distorted hallway, dutch angle.' },

            // 3. Camera Movement
            { id: 'mov_0', category: 'Camera Movement', name: 'Static', keyword: "static camera", description: 'Zero camera movement, completely still.', example: 'Static camera shot of a serene garden.' },
            { id: 'mov_1', category: 'Camera Movement', name: 'Slow Dolly Push-in', keyword: "slow dolly push-in", description: 'Camera moves physically toward the subject.', example: 'Slow dolly push-in with shallow depth of field.' },
            { id: 'mov_1_2', category: 'Camera Movement', name: 'Slow Dolly Push-out', keyword: "slow dolly push-out", description: 'Camera moves physically away from the subject.', example: 'Slow dolly push-out revealing the vast environment.' },
            { id: 'mov_2', category: 'Camera Movement', name: 'Dolly Left', keyword: "dolly left", description: 'Camera moves horizontally parallel left.', example: 'Dolly left tracking a character walking.' },
            { id: 'mov_2_2', category: 'Camera Movement', name: 'Dolly Right', keyword: "dolly right", description: 'Camera moves horizontally parallel right.', example: 'Dolly right following a runner on the track.' },
            { id: 'mov_3', category: 'Camera Movement', name: 'Handheld', keyword: "handheld camera", description: 'Intentional shaking to add visceral realism or chaos.', example: 'Handheld footage of a fast-paced market chase.' },
            { id: 'mov_4', category: 'Camera Movement', name: 'Steadicam', keyword: "steadicam tracking shot", description: 'Fluid, stable following movement.', example: 'Following a character through a nightclub, steadicam.' },
            { id: 'mov_5', category: 'Camera Movement', name: 'Crane Shot', keyword: "crane shot", description: 'Vertical movement using a large mechanical arm.', example: 'Crane shot rising from a city street to the rooftops.' },
            { id: 'mov_5_2', category: 'Camera Movement', name: 'Jib Shot', keyword: "jib shot", description: 'Sweeping boom arm movement for high perspective.', example: 'Jib shot sweeping over a crowd at a concert.' },
            { id: 'mov_6', category: 'Camera Movement', name: 'Orbiting Subject', keyword: "orbiting subject", description: 'Camera circles the subject 360 degrees.', example: 'An orbiting shot around a dancer in a rainstorm.' },
            { id: 'mov_7', category: 'Camera Movement', name: 'Whip Pan', keyword: "whip pan", description: 'Extremely fast horizontal swivel creating a blur.', example: 'A whip pan between two characters during a heated argument.' },
            { id: 'mov_8', category: 'Camera Movement', name: 'Rack Focus', keyword: "rack focus", description: 'Shifting focus from foreground to background.', example: 'A rack focus from a glass of water to a character entering the room.' },

            // 4. Lens Language
            { id: 'len_1', category: 'Lens Language', name: '14mm Ultra-Wide', keyword: "14mm ultra-wide lens", description: 'Extreme field of view, distortion, epic scale.', example: '14mm ultra-wide shot of a grand cathedral interior.' },
            { id: 'len_2', category: 'Lens Language', name: '24mm Cinematic Wide', keyword: "24mm wide-angle lens", description: 'Standard wide look for environmental scenes.', example: '24mm cinematic wide shot of a mountain range.' },
            { id: 'len_3', category: 'Lens Language', name: '35mm Natural', keyword: "35mm cinematic lens", description: 'Closest to the natural human eye perspective.', example: 'Shot on a 35mm lens, naturalistic street photography.' },
            { id: 'len_4', category: 'Lens Language', name: '50mm Portrait', keyword: "50mm portrait lens", description: 'Classic portrait feel with natural depth.', example: 'Shot on a 50mm lens with shallow depth of field.' },
            { id: 'len_5', category: 'Lens Language', name: '85mm Compressed', keyword: "85mm telephoto lens", description: 'Compresses the background, extreme separation.', example: '85mm lens, compressed background, sharp focus on subject.' },
            { id: 'len_6', category: 'Lens Language', name: 'Anamorphic', keyword: "anamorphic lens", description: 'Cinematic widescreen with oval bokeh and flares.', example: 'Shot on a 35mm anamorphic lens with cinematic bokeh.' },
            { id: 'len_7', category: 'Lens Language', name: 'Vintage Glass', keyword: "shot on vintage glass", description: 'Adds character, softness, and unique flares.', example: 'Vintage glass aesthetic with soft focus edges and warm flares.' },

            // 5. Depth of Field
            { id: 'dof_1', category: 'Depth of Field', name: 'Shallow DOF', keyword: "shallow depth of field", description: 'Background is softly blurred.', example: 'Shallow depth of field, background softly blurred.' },
            { id: 'dof_2', category: 'Depth of Field', name: 'Deep Focus', keyword: "deep focus cinematography", description: 'Everything from foreground to background is sharp.', example: 'Deep focus shot of a sprawling landscape.' },
            { id: 'dof_3', category: 'Depth of Field', name: 'Subject Separation', keyword: "strong subject separation", description: 'Subject pops out from the background.', example: 'Cinematic lighting with strong subject separation.' },
            { id: 'dof_4', category: 'Depth of Field', name: 'Background Falloff', keyword: "smooth background falloff", description: 'Gentle, natural transition from focus to blur.', example: 'Soft background falloff behind the subject.' },

            // 6. Lighting
            { id: 'lit_1', category: 'Lighting', name: 'Natural Light', keyword: "natural lighting", description: 'Sunlight or ambient environment light.', example: 'Natural lighting through an open window.' },
            { id: 'lit_1_2', category: 'Lighting', name: 'Golden Hour', keyword: "golden hour lighting", description: 'Warm, soft light just before sunset or after sunrise.', example: 'Exterior, wheat field, golden hour lighting.' },
            { id: 'lit_1_3', category: 'Lighting', name: 'Overcast Soft Light', keyword: "overcast soft light", description: 'Diffused, even lighting from a cloudy sky.', example: 'Muted forest scene with overcast soft light.' },
            { id: 'lit_2', category: 'Lighting', name: 'High-Contrast', keyword: "high-contrast lighting", description: 'Strong difference between light and dark.', example: 'High-contrast lighting with harsh shadows.' },
            { id: 'lit_3', category: 'Lighting', name: 'Low-key', keyword: "low-key lighting", description: 'Atmospheric lighting with high contrast and deep shadows.', example: 'Low-key lighting with a strong rim light.' },
            { id: 'lit_3_2', category: 'Lighting', name: 'High-key', keyword: "high-key lighting", description: 'Bright, even illumination with minimal shadows.', example: 'High-key lighting for a clean, upbeat interior scene.' },
            { id: 'lit_4', category: 'Lighting', name: 'Rim Light', keyword: "rim light", description: 'Light outlining the subject from behind.', example: 'Strong rim light outlining his face.' },
            { id: 'lit_5', category: 'Lighting', name: 'Practical Lights', keyword: "practical lamps", description: 'Lights visible in the scene (lamps, neon).', example: 'Low-key lighting with soft practical lamps.' },
            { id: 'lit_6', category: 'Lighting', name: 'Volumetric / God Rays', keyword: "volumetric light god rays", description: 'Visible beams of light through haze.', example: 'Volumetric god rays through church windows.' },
            { id: 'lit_7', category: 'Lighting', name: 'Film Noir', keyword: "film noir lighting", description: 'Moody, high-contrast, striped shadows.', example: 'Black and white, film noir lighting, Venetian blinds shadows.' },

            // 7. Color & Film Look
            { id: 'col_1', category: 'Color & Film Look', name: 'Teal & Orange', keyword: "teal and orange color grade", description: 'Cinematic contrast between cool and warm.', example: 'Blockbuster teal and orange cinematic color grading.' },
            { id: 'col_2', category: 'Color & Film Look', name: 'Bleach Bypass', keyword: "bleach bypass film look", description: 'High contrast, low saturation, gritty.', example: 'War zone scene, bleach bypass aesthetic.' },
            { id: 'col_3', category: 'Color & Film Look', name: '35mm Film Grain', keyword: "35mm film grain", description: 'Classic cinematic texture with fine grain.', example: 'Muted cinematic color grade with subtle 35mm film grain.' },
            { id: 'col_3_2', category: 'Color & Film Look', name: '16mm Film Grain', keyword: "16mm film grain", description: 'Coarse, gritty texture for a nostalgic look.', example: 'Nostalgic memories shot on gritty 16mm film.' },
            { id: 'col_4', category: 'Color & Film Look', name: 'Warm Highlights / Cool Shadows', keyword: "warm highlights and cool shadows", description: 'Standard cinematic color balance.', example: 'Warm highlights, cool shadows, cinematic atmosphere.' },

            // 8. Motion & Timing
            { id: 'mot_1', category: 'Motion & Timing', name: 'Slow Motion', keyword: "slow motion", description: 'Time is slowed for dramatic effect.', example: 'Slow motion shot of water droplets splashing.' },
            { id: 'mot_2', category: 'Motion & Timing', name: 'Dreamlike Movement', keyword: "dreamlike movement", description: 'Surreal, flowing motion.', example: 'Dreamlike movement in a misty forest.' },
            { id: 'mot_3', category: 'Motion & Timing', name: 'Realistic Physics', keyword: "realistic physics", description: 'Ensures motion follows natural laws.', example: 'Slow, deliberate movement with realistic physics.' },

            // 9. Scene & Environment
            { id: 'env_1', category: 'Scene & Environment', name: 'Interior (INT)', keyword: "interior", description: 'Specifies the scene takes place inside.', example: 'Interior, dimly lit bar at night.' },
            { id: 'env_1_2', category: 'Scene & Environment', name: 'Exterior (EXT)', keyword: "exterior", description: 'Specifies the scene takes place outside.', example: 'Exterior, night, light rain reflecting neon lights.' },
            { id: 'env_2', category: 'Scene & Environment', name: 'Weather Detail', keyword: "light rain on wet asphalt", description: 'Adds environmental texture.', example: 'Rain reflecting neon lights on wet asphalt.' },

            // 10. Quality & Realism
            { id: 'qua_1', category: 'Quality & Realism', name: 'Photorealistic', keyword: "photorealistic", description: 'High detail, looks like real life.', example: 'Photorealistic textures, cinematic realism.' },
            { id: 'qua_2', category: 'Quality & Realism', name: 'Stable Camera', keyword: "stable camera", description: 'No jitter or unintentional shaking.', example: 'Cinematic color grading, subtle film grain, realistic motion, stable camera.' },
            { id: 'qua_3', category: 'Quality & Realism', name: 'Natural Motion Blur', keyword: "natural motion blur", description: 'Realistic cinematic blurring of movement.', example: 'Professional film production, natural motion blur.' }
        ];

        let selectedIds = new Set();
        let currentView = 'all';
        let searchQuery = '';
        const apiKey = ""; 

        function toggleTheme() {
            const html = document.documentElement;
            const isDark = html.classList.toggle('dark');
            const icon = document.getElementById('theme-icon');
            const text = document.getElementById('theme-text');
            icon.className = isDark ? 'fas fa-sun' : 'fas fa-moon';
            text.innerText = isDark ? 'Light Mode' : 'Dark Mode';
            localStorage.setItem('theme', isDark ? 'dark' : 'light');
        }

        function initSidebar() {
            const categories = [...new Set(libraryData.map(i => i.category))];
            const nav = document.getElementById('category-nav');
            const datalist = document.getElementById('category-list');
            nav.innerHTML = categories.map(cat => `
                <button onclick="setView('${cat}')" id="nav-${cat.replace(/\s+/g, '-').toLowerCase()}" 
                        class="w-full flex items-center justify-between group px-3 py-2 rounded-lg text-sm font-medium transition-all hover:bg-black/5 dark:hover:bg-white/5 text-gray-500 dark:text-gray-400">
                    <div class="flex items-center gap-3">
                        <span class="w-1.5 h-1.5 rounded-full bg-gray-300 dark:bg-gray-700"></span>
                        ${cat}
                    </div>
                </button>
            `).join('');
            datalist.innerHTML = categories.map(cat => `<option value="${cat}">`).join('');
        }

        function openModal() {
            const overlay = document.getElementById('modal-overlay');
            overlay.classList.remove('hidden');
            setTimeout(() => {
                overlay.classList.add('opacity-100');
                overlay.querySelector('div').classList.replace('scale-95', 'scale-100');
            }, 10);
            if (currentView !== 'all' && currentView !== 'ai') document.getElementById('new-category').value = currentView;
        }

        function closeModal() {
            const overlay = document.getElementById('modal-overlay');
            overlay.classList.remove('opacity-100');
            overlay.querySelector('div').classList.replace('scale-100', 'scale-95');
            setTimeout(() => overlay.classList.add('hidden'), 300);
            document.getElementById('add-item-form').reset();
        }

        document.getElementById('add-item-form').onsubmit = (e) => {
            e.preventDefault();
            const category = document.getElementById('new-category').value.trim();
            const name = document.getElementById('new-name').value.trim();
            const keyword = document.getElementById('new-keyword').value.trim();
            const desc = document.getElementById('new-desc').value.trim();
            if (!category || !name || !keyword) return showToast("Please fill in category, name, and keyword.");
            libraryData.unshift({ id: 'custom_' + Date.now(), category, name, keyword, description: desc || 'Custom entry.', example: `Cinematic scene using ${keyword}.` });
            initSidebar(); closeModal(); renderContent(); showToast(`Added to ${category}`);
        };

        function setView(view) {
            currentView = view;
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('active', 'bg-black/5', 'dark:bg-white/5', 'text-blue-500'));
            const targetId = view === 'ai' ? 'nav-ai' : (view === 'all' ? 'nav-all' : `nav-${view.replace(/\s+/g, '-').toLowerCase()}`);
            const btn = document.getElementById(targetId);
            if (btn) btn.classList.add('active', 'bg-black/5', 'dark:bg-white/5', 'text-blue-500');
            const header = document.querySelector('header');
            if (view === 'ai') header.querySelector('.flex-1').classList.add('invisible');
            else header.querySelector('.flex-1').classList.remove('invisible');

            const tipEl = document.getElementById('context-tip');
            if (view === 'ai') {
                tipEl.innerText = "Tip: Combine your library selections with a scene description to generate complex AI scripts.";
            } else {
                tipEl.innerText = "Tip: For Veo 3 / video-gen models, think in terms of cinematography language, not camera brand nerd stuff.\n\nHow it works:\n1. Browse shots.\n2. Select favorites.\n3. Open AI Builder to generate a full scene prompt.";
            }
            renderContent();
        }

        function handleSearch() { searchQuery = document.getElementById('search-input').value.toLowerCase(); renderContent(); }

        function toggleSelection(id) {
            if (selectedIds.has(id)) selectedIds.delete(id);
            else selectedIds.add(id);
            updateSelectionUI(); renderContent();
        }

        function updateSelectionUI() {
            const badge = document.getElementById('selection-counter');
            const count = document.getElementById('count-num');
            if (selectedIds.size > 0) { badge.classList.remove('hidden'); count.innerText = selectedIds.size; }
            else badge.classList.add('hidden');
        }

        function renderContent() {
            const mainScroll = document.getElementById('main-scroll');
            if (!mainScroll) return;
            mainScroll.innerHTML = '';
            if (currentView === 'ai') { renderAIBuilder(mainScroll); return; }

            const container = document.createElement('div');
            container.id = 'content-area';
            container.className = "grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6 items-start";
            mainScroll.appendChild(container);

            const filtered = libraryData.filter(item => (currentView === 'all' || item.category === currentView) && (item.name.toLowerCase().includes(searchQuery) || item.description.toLowerCase().includes(searchQuery)));

            if (filtered.length === 0) {
                container.innerHTML = `<div class="col-span-full py-20 text-center"><i class="fas fa-box-open text-4xl text-gray-200 mb-4 block"></i><p class="text-gray-400 font-medium">No assets found.</p></div>`;
                return;
            }

            container.innerHTML = filtered.map(item => `
                <div class="mac-card bg-white dark:bg-mac-darkCard p-5 rounded-2xl shadow-sm border border-gray-100 dark:border-gray-800 flex flex-col h-full ${selectedIds.has(item.id) ? 'selected-ring' : ''}">
                    <div class="flex justify-between items-start mb-4">
                        <span class="text-[10px] font-bold uppercase tracking-widest text-blue-500 bg-blue-50 dark:bg-blue-900/20 px-2 py-1 rounded">${item.category}</span>
                        <button onclick="toggleSelection('${item.id}')" class="text-xs font-bold transition-all ${selectedIds.has(item.id) ? 'text-blue-500' : 'text-gray-400 hover:text-blue-500'}">
                            ${selectedIds.has(item.id) ? '<i class="fas fa-check-circle text-lg"></i>' : '<i class="far fa-plus-square text-lg"></i>'}
                        </button>
                    </div>
                    <h3 class="text-base font-bold mb-1">${item.name}</h3>
                    <p class="text-xs text-gray-500 dark:text-gray-400 mb-4 flex-grow">${item.description}</p>
                    <div class="bg-gray-50 dark:bg-black/20 p-3 rounded-xl border border-gray-100 dark:border-white/5 italic text-[11px] text-gray-400 dark:text-gray-500 mb-4">"${item.example}"</div>
                    <button onclick="copyToClipboard('${item.keyword}')" class="w-full py-2 bg-gray-100 dark:bg-white/5 hover:bg-gray-200 dark:hover:bg-white/10 rounded-lg text-[11px] font-bold transition-colors">Copy Keyword</button>
                </div>
            `).join('');
        }

        function renderAIBuilder(targetElement) {
            const selectedItems = libraryData.filter(i => selectedIds.has(i.id));
            targetElement.innerHTML = `
                <div class="max-w-4xl mx-auto w-full space-y-8 animate-in fade-in duration-500">
                    <div class="text-center space-y-2">
                        <h2 class="text-3xl font-extrabold tracking-tight">AI Director</h2>
                        <p class="text-gray-500 dark:text-gray-400">Combine selected optics, lighting, and film science into a master script.</p>
                    </div>
                    <div class="bg-white dark:bg-mac-darkCard rounded-3xl p-8 shadow-xl border border-gray-100 dark:border-gray-800 space-y-6">
                        <div>
                            <label class="block text-[11px] font-bold text-gray-400 uppercase tracking-widest mb-3">Active Cinematic Profile</label>
                            <div class="flex flex-wrap gap-2">
                                ${selectedItems.length ? selectedItems.map(i => `<span class="bg-blue-500/10 text-blue-600 dark:text-blue-400 px-3 py-1.5 rounded-full text-xs font-semibold flex items-center gap-2 border border-blue-500/20">${i.name} <button onclick="toggleSelection('${i.id}')" class="hover:text-blue-800"><i class="fas fa-times text-[10px]"></i></button></span>`).join('') : '<p class="text-sm text-gray-400 italic">No assets selected.</p>'}
                            </div>
                        </div>
                        <div>
                            <label class="block text-[11px] font-bold text-gray-400 uppercase tracking-widest mb-3">Scene Description</label>
                            <textarea id="ai-input" placeholder="e.g. 'A tired man sitting alone in a dimly lit bar at night...'" class="w-full h-32 p-4 bg-gray-50 dark:bg-black/40 rounded-2xl border border-gray-200 dark:border-gray-800 outline-none focus:ring-2 focus:ring-blue-500 transition-all text-sm resize-none dark:text-white"></textarea>
                        </div>
                        <div class="p-4 bg-gray-100 dark:bg-white/5 rounded-2xl border border-gray-200 dark:border-gray-800">
                           <p class="text-[10px] font-bold text-red-500 uppercase mb-2"><i class="fas fa-exclamation-triangle"></i> Director's Note (What to AVOID)</p>
                           <p class="text-[10px] text-gray-400">Avoid: Camera brands (RED/ARRI), Sensor specs, ISO, Shutter Speed, Aperture numbers, or overloading with buzzwords.</p>
                        </div>
                        <button onclick="generatePrompt()" id="gen-btn" class="w-full py-4 ai-gradient text-white rounded-2xl font-bold shadow-lg hover:opacity-90 flex items-center justify-center gap-3">Generate Professional Prompt</button>
                    </div>
                    <div id="ai-output-container" class="hidden animate-in slide-in-from-bottom-4 duration-500">
                        <div class="flex items-center justify-between mb-3 px-2">
                            <label class="text-[11px] font-bold text-gray-400 uppercase tracking-widest">Master Prompt</label>
                            <button onclick="copyGenerated()" class="text-blue-500 text-xs font-bold hover:underline">Copy All</button>
                        </div>
                        <div class="bg-black text-white p-6 rounded-3xl font-mono text-xs leading-relaxed border border-white/10 relative group shadow-2xl"><pre id="ai-output" class="whitespace-pre-wrap"></pre></div>
                    </div>
                </div>
            `;
        }

        async function generatePrompt() {
            const input = document.getElementById('ai-input').value;
            if (!input) return showToast("Enter a scene description first.");
            const btn = document.getElementById('gen-btn');
            btn.disabled = true; btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> Visualizing...';
            const keywords = libraryData.filter(i => selectedIds.has(i.id)).map(i => i.keyword).join(', ');
            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
                    method: 'POST', headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ 
                        contents: [{ parts: [{ text: `Narrative: ${input}` }] }], 
                        systemInstruction: { parts: [{ text: `You are a professional Hollywood Director. Write a high-detail video prompt for AI generation. 
                        Mandatory keywords to incorporate: ${keywords}. 
                        Follow the Veo 3 "Strong Prompt" structure: 
                        1. Framing/Action.
                        2. Camera movement & Lens.
                        3. Lighting detail.
                        4. Color grade, texture, and stability flags.
                        DO NOT use camera brands, sensor specs, or camera setting numbers (ISO/F-Stop).` }] } 
                    })
                });
                const data = await response.json();
                document.getElementById('ai-output-container').classList.remove('hidden');
                document.getElementById('ai-output').innerText = data.candidates?.[0]?.content?.parts?.[0]?.text || "Error.";
                document.getElementById('ai-output').scrollIntoView({ behavior: 'smooth' });
            } catch (err) { showToast("AI connection failed."); }
            finally { btn.disabled = false; btn.innerHTML = 'Generate Professional Prompt'; }
        }

        function copyToClipboard(text) {
            const el = document.createElement('textarea'); el.value = text; document.body.appendChild(el); el.select(); document.execCommand('copy'); document.body.removeChild(el); showToast(`Copied!`);
        }

        function copyGenerated() { if (document.getElementById('ai-output')) copyToClipboard(document.getElementById('ai-output').innerText); }
        function showToast(msg) { const t = document.getElementById('toast'); t.innerText = msg; t.style.opacity = '1'; setTimeout(() => t.style.opacity = '0', 2000); }

        window.onload = () => {
            initSidebar();
            const html = document.documentElement; html.classList.add('dark');
            document.getElementById('theme-icon').className = 'fas fa-sun';
            document.getElementById('theme-text').innerText = 'Light Mode';
            setView('all');
        };
    </script>
</body>
</html>
