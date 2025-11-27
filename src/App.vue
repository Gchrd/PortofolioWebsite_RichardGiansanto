<script setup>
import { ref, onMounted, nextTick, onUnmounted } from 'vue';
import { createIcons, icons } from 'lucide';

// REFS
const starCanvas = ref(null);

// ANIMATION LOGIC
let animationFrameId;

const initRain = () => {
    const canvas = starCanvas.value;
    if (!canvas) return;
    
    const ctx = canvas.getContext('2d');
    let width, height;
    const drops = [];
    const dropCount = 150; // Jumlah partikel hujan

    const resize = () => {
        width = window.innerWidth;
        height = window.innerHeight;
        canvas.width = width;
        canvas.height = height;
    };

    class RainDrop {
        constructor() {
            this.reset(true);
        }
        
        reset(initial = false) {
            this.x = Math.random() * (width + 200); // Buffer lebar agar hujan miring tidak putus di kanan
            this.y = initial ? Math.random() * height : -20;
            this.length = Math.random() * 20 + 10; 
            this.speed = Math.random() * 10 + 5; 
            this.opacity = Math.random() * 0.2 + 0.05; // LEBIH TRANSPARAN (0.05 - 0.25)
            this.slant = -2; // Miring ke kiri
        }

        update() {
            this.y += this.speed;
            this.x += this.slant; // Gerak miring
            
            if (this.y > height || this.x < -50) {
                this.reset();
            }
        }

        draw() {
            ctx.strokeStyle = `rgba(255, 255, 255, ${this.opacity})`;
            ctx.lineWidth = 1.5;
            ctx.lineCap = 'round';
            ctx.beginPath();
            ctx.moveTo(this.x, this.y);
            ctx.lineTo(this.x + (this.slant * 3), this.y + this.length); // Gambar garis miring
            ctx.stroke();
        }
    }

    // Listeners
    window.addEventListener('resize', resize);
    resize(); // Init size FIRST

    // Buat hujan
    for (let i = 0; i < dropCount; i++) {
        drops.push(new RainDrop());
    }

    const animate = () => {
        ctx.clearRect(0, 0, width, height);
        drops.forEach(drop => {
            drop.update();
            drop.draw();
        });
        animationFrameId = requestAnimationFrame(animate);
    };

    animate(); // Start loop

    // Cleanup function
    return () => {
        window.removeEventListener('resize', resize);
        cancelAnimationFrame(animationFrameId);
    };
};

// DATA
const profile = {
    name: "Richard Giansanto",
    role: "Web Development | AI/ML Enthusiast",
    contact: {
        phone: "+62 813 8577 0632",
        whatsappRaw: "6281385770632",
        email: "richardgiansanto111@gmail.com",
        linkedin: "https://www.linkedin.com/in/richard-giansanto/",
        github: "https://github.com/", 
        location: "Tangerang City, Banten, Indonesia"
    }
};

const navItems = [
    { name: "Home", href: "#home" },
    { name: "Skills", href: "#skills" },
    { name: "Projects", href: "#projects" },
    { name: "Experience", href: "#experience" },
    { name: "About Me", href: "#about" }, 
];

const education = [
    {
        school: "Universitas Multimedia Nusantara",
        degree: "Bachelor in Informatics",
        period: "Aug 2023 - Aug 2027 (Expected)",
        gpa: "3.92/4.00"
    },
    {
        school: "SMA Strada Santo Thomas Aquino",
        degree: "High School Diploma",
        period: "Jul 2020 - Jun 2023",
        gpa: null
    }
];

const skills = {
    hard: ["Java", "C", "C#", "Kotlin", "JavaScript", "SQL", "Node.js", "Python", "Android Studio", "Unity", "MySQL", "Git"],
    soft: ["Problem Solving", "Analytical Thinking", "Teamwork", "Communication", "Time Management", "Adaptability"]
};

const experience = [
    {
        role: "Treasurer",
        organization: "Mister & Miss UMN 2025",
        period: "Mar 2025 - Present",
        desc: "Managed committee cash flow, prepared budget plans for divisions, and produced financial reports."
    },
    {
        role: "Coordinator of Insurer",
        organization: "Infinite 2024 (PPIF)",
        period: "Aug 2024 - Nov 2024",
        desc: "Monitored participant flow, ensured event safety, and coordinated on-site issue resolutions."
    },
    {
        role: "Insurer Team Member",
        organization: "OMB UMN 2024",
        period: "Dec 2023 - Jul 2024",
        desc: "Assisted with event rundown preparation, technical needs, logistics, and participant flow management."
    }
];

const projects = [
    {
        title: "Israel-Palestine Sentiment Analysis",
        type: "Machine Learning",
        desc: "Reddit data windowing pipeline, text preprocessing (cleaning, tokenization, stemming), and automatic sentiment labeling using TextBlob for 2024-2025 datasets.",
        stack: ["Python", "Pandas", "TextBlob", "Jupyter"],
        icon: "bar-chart-2",
        link: "https://github.com/Gr1cLev/Israel-Palestine-Sentiment-Analysis"
    },
    {
        title: "LeftAlone - Survival Horror Game",
        type: "Game Development",
        desc: "First-person survival horror game with battery management, patrolling monster AI, and multiple win/lose conditions. Built with Unity and C# scripting.",
        stack: ["Unity", "C#", "AI Logic", "3D Audio"],
        icon: "gamepad-2",
        link: "https://github.com/Gchrd/LeftAloneGames-Unity"
    },
    {
        title: "News App Mobile",
        type: "Mobile App",
        desc: "Android news application focused on reading experience. Implemented JSON parsing from API, intent navigation, and responsive layouts.",
        stack: ["Kotlin", "Android Studio", "JSON API", "UI/UX"],
        icon: "smartphone",
        link: null
    }
];

const certifications = [
    { name: "AI Productivity & AI API Integration", issuer: "Hacktiv8 Indonesia", date: "Oct 2025" },
    { name: "AI Made Simple", issuer: "Bitslab Academy", date: "Sep 2025" },
    { name: "Python Intermediate", issuer: "Sololearn", date: "Apr 2025" }
];

let cleanupStars;

// CURSOR LOGIC
const mouseX = ref(0);
const mouseY = ref(0);
const trail = ref([]);
const isHovering = ref(false);
let trailInterval;

const updateCursor = (e) => {
    mouseX.value = e.clientX;
    mouseY.value = e.clientY;
    
    // Check hover state for clickable elements
    const target = e.target;
    isHovering.value = target.closest('a, button, input, textarea') !== null;

    // Add point to trail
    trail.value.push({ 
        x: e.clientX, 
        y: e.clientY, 
        time: Date.now(),
        id: Math.random() 
    });
};

const initCursor = () => {
    window.addEventListener('mousemove', updateCursor);
    
    const animateTrail = () => {
        const now = Date.now();
        // Keep points younger than 100ms for faster, smoother disappearance
        trail.value = trail.value.filter(p => now - p.time < 100);
        trailInterval = requestAnimationFrame(animateTrail);
    };
    animateTrail();
};

const observeElements = () => {
    const observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
            if (entry.isIntersecting) {
                entry.target.classList.add('active');
            } else {
                // Remove active class when element leaves viewport to trigger exit animation
                entry.target.classList.remove('active');
            }
        });
    }, {
        threshold: 0.15, // Sedikit lebih besar agar tidak flicker di pinggir
        rootMargin: "0px 0px -50px 0px"
    });

    document.querySelectorAll('.reveal').forEach((el) => {
        observer.observe(el);
    });
};

onMounted(() => {
    nextTick(() => {
        createIcons({ icons });
        // Initialize Rain Animation
        if(starCanvas.value) {
            cleanupStars = initRain();
        }
        // Initialize Scroll Animation
        observeElements();
        // Initialize Custom Cursor
        initCursor();
    });
});

onUnmounted(() => {
    if (cleanupStars) cleanupStars();
    window.removeEventListener('mousemove', updateCursor);
    if (trailInterval) cancelAnimationFrame(trailInterval);
});
</script>

<template>
    <div class="relative min-h-screen bg-gradient-to-br from-[#050505] via-[#1a0505] to-[#050505] text-neutral-300 antialiased selection:bg-red-600 selection:text-white overflow-x-hidden">
        
        <!-- Custom Cursor -->
        <div 
            class="fixed top-0 left-0 w-4 h-4 bg-red-500 rounded-full pointer-events-none z-[9999] transform -translate-x-1/2 -translate-y-1/2 mix-blend-screen transition-transform duration-100 shadow-[0_0_10px_rgba(239,68,68,0.8)]"
            :class="{ 'scale-[2.5] bg-white mix-blend-difference': isHovering }"
            :style="{ left: `${mouseX}px`, top: `${mouseY}px` }"
        ></div>

        <!-- Cursor Trail -->
        <div 
            v-for="(point, index) in trail" 
            :key="point.id"
            class="fixed top-0 left-0 w-3 h-3 bg-red-500/40 rounded-full pointer-events-none z-[9998] transform -translate-x-1/2 -translate-y-1/2"
            :style="{ 
                left: `${point.x}px`, 
                top: `${point.y}px`,
                opacity: (index / trail.length),
                transform: `translate(-50%, -50%) scale(${index / trail.length})`
            }"
        ></div>

        <!-- UPDATE: Canvas untuk Animasi Bintang -->
        <canvas ref="starCanvas" class="fixed inset-0 w-full h-full pointer-events-none z-0 opacity-80"></canvas>

        <!-- Navigation -->
        <nav class="fixed w-full z-50 bg-[#0a0a0a]/80 backdrop-blur-md border-b border-white/5">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                <div class="flex items-center justify-between h-20">
                    <div class="flex-shrink-0">
                        <span class="text-2xl font-bold font-heading text-white tracking-tight">Richard G</span>
                    </div>
                    <div class="hidden md:block">
                        <div class="ml-10 flex items-center space-x-8">
                            <a v-for="item in navItems" :key="item.name" :href="item.href" class="hover:text-red-500 hover:bg-white/5 transition-all px-4 py-2 rounded-full text-sm font-medium">
                                {{ item.name }}
                            </a>
                            
                            <!-- Resume Button -->
                            <a href="/Richard Giansanto_resume.pdf" target="_blank" class="flex items-center gap-2 px-5 py-2 border border-red-600 text-red-500 rounded-full text-sm font-bold hover:bg-red-600 hover:text-white transition-all transform hover:scale-105 shadow-[0_0_15px_rgba(220,38,38,0.2)]">
                                <i data-lucide="file-text" class="w-4 h-4"></i>
                                Resume
                            </a>
                        </div>
                    </div>
                    <!-- Mobile menu button placeholder -->
                    <div class="-mr-2 flex md:hidden">
                        <button class="text-gray-400 hover:text-white p-2">
                            <i data-lucide="menu"></i>
                        </button>
                    </div>
                </div>
            </div>
        </nav>

        <!-- Hero Section -->
        <section id="home" class="min-h-screen flex items-center justify-center pt-24 pb-12 relative overflow-hidden z-10">
            <!-- Background Decoration -->
            <div class="absolute top-0 right-0 w-96 h-96 bg-red-600/10 rounded-full blur-[100px] -z-10 opacity-60 translate-x-1/3 -translate-y-1/3"></div>
            <div class="absolute bottom-0 left-0 w-96 h-96 bg-blue-900/10 rounded-full blur-[100px] -z-10 opacity-40 -translate-x-1/3 translate-y-1/3"></div>
            
            <div class="max-w-6xl mx-auto px-6 md:px-20 lg:px-32 grid grid-cols-1 md:grid-cols-2 gap-12 items-center">
                <!-- Text Content -->
                <div class="text-left reveal">
                    <p class="text-red-500 font-medium mb-3 text-sm tracking-[0.2em] uppercase font-heading">Hello, I am</p>
                    <h1 class="text-4xl md:text-6xl font-bold text-white mb-4 leading-tight">
                        {{ profile.name }}
                    </h1>
                    <h2 class="text-lg md:text-xl text-neutral-400 mb-6 font-light">
                        {{ profile.role }}
                    </h2>
                    <p class="text-base text-neutral-400 max-w-xl mb-8 leading-relaxed">
                        Passionate about building Android apps, Web platforms, and integrating AI/ML solutions to solve real-world problems.
                    </p>
                    <div class="flex justify-start gap-4">
                        <a href="#projects" class="px-8 py-3 bg-red-600 hover:bg-red-700 text-white font-bold rounded-full transition-all hover:scale-105 shadow-[0_0_20px_rgba(220,38,38,0.3)] text-sm">
                            View Projects
                        </a>
                        <a href="#about" class="px-8 py-3 border border-red-600/50 text-red-500 hover:bg-red-600/10 font-bold rounded-full transition-all text-sm">
                            Contact Me
                        </a>
                    </div>
                    
                    <!-- Social Links -->
                    <div class="mt-10 flex justify-start space-x-6">
                        <a :href="profile.contact.linkedin" target="_blank" class="text-neutral-500 hover:text-red-500 transition-colors hover:-translate-y-1 transform duration-200">
                            <i data-lucide="linkedin" class="w-5 h-5"></i>
                        </a>
                        <a :href="'mailto:' + profile.contact.email" class="text-neutral-500 hover:text-red-500 transition-colors hover:-translate-y-1 transform duration-200">
                            <i data-lucide="mail" class="w-5 h-5"></i>
                        </a>
                    </div>
                </div>

                <!-- Profile Image -->
                <div class="flex justify-center md:justify-end reveal">
                    <div class="relative w-48 h-48 sm:w-72 sm:h-72">
                        <!-- Glow Effect -->
                        <div class="absolute inset-0 bg-gradient-to-tr from-red-600 to-orange-600 rounded-full rotate-6 opacity-50 blur-lg animate-pulse"></div>
                        
                        <!-- Image Container -->
                        <div class="relative w-full h-full rounded-full overflow-hidden border-2 border-white/10 shadow-2xl group flex items-center justify-center">
                            <img src="/FotoProfil.png" alt="Richard Giansanto" class="w-full h-full object-cover scale-125 group-hover:scale-150 transition-transform duration-700">
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Skills Section (01) -->
        <section id="skills" class="py-24 relative z-10">
            <!-- Background Decoration -->
            <div class="absolute inset-0 bg-neutral-900/10 skew-y-3 -z-10 backdrop-blur-[2px]"></div>
            
            <div class="max-w-6xl mx-auto px-6 md:px-12">
                <div class="flex items-end gap-4 mb-16 reveal">
                    <h2 class="text-4xl md:text-5xl font-bold text-white">
                        <span class="text-red-600 text-lg align-top font-mono">01.</span> Skills
                    </h2>
                    <div class="h-px bg-red-900/50 flex-grow mb-4 ml-4"></div>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-16">
                    <!-- Hard Skills -->
                    <div class="bg-white/5 p-8 rounded-3xl border border-white/5 backdrop-blur-md hover:border-red-500/30 transition-colors duration-300 reveal">
                        <h3 class="text-xl font-bold text-white mb-8 flex items-center gap-3">
                            <div class="p-2 bg-red-500/10 rounded-lg text-red-500">
                                <i data-lucide="cpu" class="w-5 h-5"></i>
                            </div>
                            Core Technologies
                        </h3>
                        <div class="flex flex-wrap gap-3">
                            <span v-for="skill in skills.hard" :key="skill" class="px-5 py-2.5 bg-[#0a0a0a]/50 text-neutral-300 rounded-full text-sm font-medium border border-white/10 hover:border-red-500 hover:text-white hover:shadow-[0_0_15px_rgba(239,68,68,0.3)] transition-all cursor-default group">
                                <span class="group-hover:text-red-400 mr-1">#</span>{{ skill }}
                            </span>
                        </div>
                    </div>

                    <!-- Soft Skills -->
                    <div class="bg-white/5 p-8 rounded-3xl border border-white/5 backdrop-blur-md hover:border-blue-500/30 transition-colors duration-300 reveal">
                        <h3 class="text-xl font-bold text-white mb-8 flex items-center gap-3">
                            <div class="p-2 bg-blue-500/10 rounded-lg text-blue-500">
                                <i data-lucide="zap" class="w-5 h-5"></i>
                            </div>
                            Professional Attributes
                        </h3>
                        <div class="space-y-4">
                            <div v-for="skill in skills.soft" :key="skill" class="flex items-center gap-4 group">
                                <div class="h-1.5 w-1.5 rounded-full bg-neutral-600 group-hover:bg-blue-400 transition-colors"></div>
                                <span class="text-neutral-400 group-hover:text-white transition-colors text-lg">{{ skill }}</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Projects Section (02) -->
        <section id="projects" class="py-24 z-10 relative">
            <div class="max-w-7xl mx-auto px-6 md:px-12">
                <div class="flex items-end gap-4 mb-16 justify-end text-right reveal">
                    <div class="h-px bg-red-900/50 flex-grow mb-4 mr-4"></div>
                    <h2 class="text-4xl md:text-5xl font-bold text-white">
                        Projects <span class="text-red-600 text-lg align-top font-mono">02.</span>
                    </h2>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
                    <component 
                        :is="project.link ? 'a' : 'div'" 
                        v-for="(project, index) in projects" 
                        :key="index" 
                        :href="project.link"
                        :target="project.link ? '_blank' : null"
                        :class="[
                            'group relative bg-[#121212]/80 backdrop-blur-sm rounded-3xl overflow-hidden border border-white/5 transition-all duration-300 flex flex-col h-full reveal',
                            project.link ? 'hover:border-red-500/50 hover:-translate-y-2 cursor-pointer' : 'opacity-80'
                        ]"
                    >
                        <div class="p-8 h-full flex flex-col relative z-10">
                            <div class="flex justify-between items-start mb-6">
                                <div class="p-3 bg-white/5 rounded-2xl group-hover:bg-red-600 group-hover:text-white transition-colors duration-300 text-red-500">
                                    <i :data-lucide="project.icon" class="w-8 h-8"></i>
                                </div>
                                <div class="px-3 py-1 bg-white/5 rounded-full text-xs font-mono text-neutral-500 border border-white/5">
                                    {{ project.type }}
                                </div>
                            </div>
                            
                            <h3 class="text-2xl font-bold text-white mb-4 group-hover:text-red-400 transition-colors flex items-center gap-2">
                                {{ project.title }}
                                <i v-if="project.link" data-lucide="external-link" class="w-4 h-4 opacity-0 group-hover:opacity-100 transition-opacity text-red-400"></i>
                            </h3>
                            <p class="text-neutral-400 text-sm leading-relaxed mb-8 flex-grow">
                                {{ project.desc }}
                            </p>
                            
                            <div class="pt-6 border-t border-white/5">
                                <div class="flex flex-wrap gap-2">
                                    <span v-for="tech in project.stack" :key="tech" class="text-xs font-mono text-neutral-400 bg-white/5 px-2 py-1 rounded hover:bg-white/10 transition-colors">
                                        {{ tech }}
                                    </span>
                                </div>
                            </div>
                        </div>
                        
                        <div v-if="project.link" class="absolute inset-0 bg-gradient-to-br from-red-900/20 to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-500 pointer-events-none"></div>
                    </component>
                </div>
            </div>
        </section>

        <!-- Experience & Education (03) -->
        <!-- UPDATE: Menggunakan background semi-transparan (opacity 90%) agar animasi bintang terlihat -->
        <section id="experience" class="py-24 bg-[#0d0d0d]/90 relative overflow-hidden z-10 backdrop-blur-sm">
             <div class="absolute inset-0 bg-[radial-gradient(#ffffff05_1px,transparent_1px)] [background-size:20px_20px] opacity-20"></div>

            <div class="max-w-6xl mx-auto px-6 md:px-12 relative z-10">
                <div class="flex items-center gap-4 mb-20 reveal">
                    <h2 class="text-4xl md:text-5xl font-bold text-white">
                        <span class="text-red-600 text-lg align-top font-mono">03.</span> Experience
                    </h2>
                    <div class="h-px bg-red-900/50 w-24"></div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-2 gap-20">
                    
                    <!-- Education Column -->
                    <div class="relative reveal">
                        <h3 class="text-2xl font-bold text-white mb-10 flex items-center gap-3">
                            <span class="w-8 h-8 rounded-lg bg-red-600/20 flex items-center justify-center text-red-500">
                                <i data-lucide="graduation-cap" class="w-4 h-4"></i>
                            </span>
                            Education
                        </h3>
                        <div class="absolute left-[15px] top-16 bottom-0 w-px bg-gradient-to-b from-red-600/50 to-transparent"></div>

                        <div class="space-y-12">
                            <div v-for="(edu, index) in education" :key="index" class="relative pl-12 group">
                                <div class="absolute left-[11px] top-2 w-2.5 h-2.5 rounded-full bg-[#0d0d0d] border-2 border-red-600 group-hover:scale-125 group-hover:bg-red-600 transition-all z-10"></div>
                                
                                <div class="group-hover:translate-x-2 transition-transform duration-300">
                                    <span class="text-xs font-mono text-red-500 mb-1 block">{{ edu.period }}</span>
                                    <h4 class="text-xl font-bold text-white mb-1">{{ edu.school }}</h4>
                                    <p class="text-neutral-400 text-sm mb-2">{{ edu.degree }}</p>
                                    <span v-if="edu.gpa" class="inline-flex items-center px-2.5 py-0.5 rounded text-xs font-medium bg-red-900/20 text-red-400 border border-red-900/30">
                                        GPA: {{ edu.gpa }}
                                    </span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Organization Column -->
                    <div class="relative reveal">
                        <h3 class="text-2xl font-bold text-white mb-10 flex items-center gap-3">
                            <span class="w-8 h-8 rounded-lg bg-blue-600/20 flex items-center justify-center text-blue-500">
                                <i data-lucide="users" class="w-4 h-4"></i>
                            </span>
                            Organization
                        </h3>
                         <div class="absolute left-[15px] top-16 bottom-0 w-px bg-gradient-to-b from-blue-600/50 to-transparent"></div>

                        <div class="space-y-12">
                            <div v-for="(exp, index) in experience" :key="index" class="relative pl-12 group">
                                 <div class="absolute left-[11px] top-2 w-2.5 h-2.5 rounded-full bg-[#0d0d0d] border-2 border-blue-600 group-hover:scale-125 group-hover:bg-blue-600 transition-all z-10"></div>

                                <div class="bg-white/5 p-6 rounded-2xl border border-white/5 hover:border-blue-500/30 transition-all duration-300 group-hover:bg-white/[0.07]">
                                    <div class="flex justify-between items-start mb-3">
                                        <h4 class="text-lg font-bold text-white">{{ exp.role }}</h4>
                                        <span class="text-xs font-mono text-neutral-500 bg-black/30 px-2 py-1 rounded">{{ exp.period }}</span>
                                    </div>
                                    <p class="text-blue-400 text-sm font-medium mb-3">{{ exp.organization }}</p>
                                    <p class="text-neutral-400 text-sm leading-relaxed border-t border-white/5 pt-3">
                                        {{ exp.desc }}
                                    </p>
                                </div>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
        </section>

        <!-- Certifications -->
        <section id="certs" class="py-24 border-t border-white/5 relative z-10 bg-[#0a0a0a]/50 backdrop-blur-sm">
            <div class="max-w-5xl mx-auto px-6 text-center reveal">
                <h2 class="text-3xl font-bold text-white mb-12 font-heading">Certifications & Courses</h2>
                <div class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
                    <div v-for="(cert, index) in certifications" :key="index" class="p-6 rounded-2xl bg-gradient-to-b from-neutral-900/80 to-black/80 border border-white/10 hover:border-red-500/50 transition-all hover:-translate-y-1 group text-left flex flex-col h-full justify-between">
                        <div>
                            <div class="mb-4 text-neutral-600 group-hover:text-red-500 transition-colors">
                                <i data-lucide="award" class="w-8 h-8"></i>
                            </div>
                            <h4 class="text-white font-semibold text-sm leading-snug mb-2">{{ cert.name }}</h4>
                        </div>
                        <div class="mt-4 pt-4 border-t border-white/5">
                            <p class="text-neutral-500 text-xs uppercase tracking-wider mb-1">{{ cert.issuer }}</p>
                            <p class="text-red-500 text-xs font-mono">{{ cert.date }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- About & Contact (04) -->
        <!-- UPDATE: Menggunakan background hitam semi-transparan -->
        <section id="about" class="py-24 relative overflow-hidden bg-black/90 z-10 backdrop-blur-sm">
            <div class="absolute bottom-0 left-0 right-0 h-full bg-gradient-to-t from-red-900/10 to-transparent pointer-events-none"></div>

            <div class="max-w-4xl mx-auto px-6 relative z-10">
                <div class="text-center mb-16 reveal">
                    <span class="text-red-600 font-mono text-sm mb-4 block">04.</span>
                    <h2 class="text-5xl md:text-6xl font-bold text-white mb-8 tracking-tight">About Me</h2>
                    <p class="text-neutral-400 text-lg leading-relaxed max-w-2xl mx-auto">
                        I am currently looking for internship opportunities in Software Engineering or AI. 
                        Whether you have a question or just want to say hi, my inbox is always open!
                    </p>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 reveal">
                    <!-- WhatsApp -->
                    <a :href="'https://wa.me/' + profile.contact.whatsappRaw" target="_blank" class="flex items-center p-6 rounded-2xl bg-[#111]/80 border border-white/5 hover:border-green-500/50 hover:bg-[#151515] transition-all group">
                        <div class="w-12 h-12 rounded-full bg-green-900/20 flex items-center justify-center text-green-500 group-hover:scale-110 transition-transform">
                            <i data-lucide="phone" class="w-6 h-6"></i>
                        </div>
                        <div class="ml-5">
                            <p class="text-sm text-neutral-500 mb-1">Chat on WhatsApp</p>
                            <p class="text-white font-medium text-lg">{{ profile.contact.phone }}</p>
                        </div>
                    </a>

                    <!-- Email -->
                    <a :href="'mailto:' + profile.contact.email" class="flex items-center p-6 rounded-2xl bg-[#111]/80 border border-white/5 hover:border-blue-500/50 hover:bg-[#151515] transition-all group">
                        <div class="w-12 h-12 rounded-full bg-blue-900/20 flex items-center justify-center text-blue-500 group-hover:scale-110 transition-transform">
                            <i data-lucide="mail" class="w-6 h-6"></i>
                        </div>
                        <div class="ml-5">
                            <p class="text-sm text-neutral-500 mb-1">Send an Email</p>
                            <p class="text-white font-medium text-lg truncate w-48 sm:w-auto">{{ profile.contact.email }}</p>
                        </div>
                    </a>

                    <!-- LinkedIn -->
                    <a :href="profile.contact.linkedin" target="_blank" class="flex items-center p-6 rounded-2xl bg-[#111]/80 border border-white/5 hover:border-blue-400/50 hover:bg-[#151515] transition-all group md:col-span-1">
                        <div class="w-12 h-12 rounded-full bg-blue-700/20 flex items-center justify-center text-blue-400 group-hover:scale-110 transition-transform">
                            <i data-lucide="linkedin" class="w-6 h-6"></i>
                        </div>
                        <div class="ml-5">
                            <p class="text-sm text-neutral-500 mb-1">Professional Network</p>
                            <p class="text-white font-medium text-lg">Connect on LinkedIn</p>
                        </div>
                    </a>

                    <!-- GitHub -->
                    <a :href="profile.contact.github" target="_blank" class="flex items-center p-6 rounded-2xl bg-[#111]/80 border border-white/5 hover:border-white/50 hover:bg-[#151515] transition-all group md:col-span-1">
                        <div class="w-12 h-12 rounded-full bg-white/10 flex items-center justify-center text-white group-hover:scale-110 transition-transform">
                            <i data-lucide="github" class="w-6 h-6"></i>
                        </div>
                        <div class="ml-5">
                            <p class="text-sm text-neutral-500 mb-1">Code Repository</p>
                            <p class="text-white font-medium text-lg">Check my Repos</p>
                        </div>
                    </a>
                </div>
            </div>
        </section>

        <!-- Footer -->
        <footer class="py-8 bg-black border-t border-white/5 text-center relative z-10">
            <p class="text-neutral-600 text-xs font-mono">
                &copy; 2025 Richard Giansanto. <span class="text-red-900 mx-2">///</span> Built with Vue.js & Tailwind
            </p>
        </footer>
    </div>
</template>
