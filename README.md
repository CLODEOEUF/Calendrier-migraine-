<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Suivi Migraine - Édition Journal Ocre Large</title>
    <!-- Chargement de Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'bg-paper': '#FDFBF7', // Fond crème
                        'text-ink': '#1a1816', // Noir encre profond
                        'terra-500': '#D96C4E', // Terre cuite vibrante
                        'terra-600': '#B54B32', // Terre cuite foncée
                        'terra-100': '#F2DMD9', // Terre cuite pâle
                        'sage': '#6b9c85', // Vert sauge
                        'sand': '#E6E0D0', // Sable foncé
                    },
                    fontFamily: {
                        serif: ['Georgia', 'Cambria', 'Times New Roman', 'Times', 'serif'],
                        sans: ['Helvetica', 'Arial', 'sans-serif'],
                    }
                }
            }
        }
    </script>
    <!-- Chargement de React et ReactDOM -->
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <!-- Chargement de Babel -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <!-- Chargement de html2canvas -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    
    <style>
        body { background-color: #F2DMD9; /* Fond extérieur terra-100 */ color: #1a1816; }
        input[type="range"] { height: 24px; accent-color: #D96C4E; }
        button, input, select { touch-action: manipulation; }
        .fade-in { animation: fadeIn 0.5s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        ::-webkit-scrollbar { width: 0px; background: transparent; }
        
        /* Masque pour l'image perso en mode normal */
        .custom-image-mask {
            mask-image: linear-gradient(to bottom right, black 60%, transparent 100%);
            -webkit-mask-image: linear-gradient(to bottom right, black 60%, transparent 100%);
        }
    </style>
</head>
<body class="min-h-screen pb-safe flex items-center justify-center p-2 md:p-4">
    <div id="root" class="w-full h-full max-w-5xl"></div>

    <script type="text/babel">
        // --- Illustrations Artistiques ---
        const Art = {
            TinyFlower: (props) => (
                <svg {...props} viewBox="0 0 50 50" fill="none" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round">
                    <path d="M25,45 Q25,25 25,25 M25,25 Q15,15 15,10 Q25,15 25,25 Q35,15 35,10 Q25,20 25,25 M25,25 Q20,30 10,25 Q20,25 25,25 M25,25 Q30,30 40,25 Q30,25 25,25" />
                </svg>
            ),
            BirdLine: (props) => (
                <svg {...props} viewBox="0 0 100 100" fill="none" stroke="currentColor" strokeWidth="1.2" strokeLinecap="round" strokeLinejoin="round">
                    <path d="M10,50 Q30,30 50,50 T90,40 M50,50 Q55,65 45,70 Q35,75 30,65" />
                </svg>
            ),
            LeafLine: (props) => (
                <svg {...props} viewBox="0 0 100 100" fill="none" stroke="currentColor" strokeWidth="1.2">
                    <path d="M50,90 Q50,50 80,20 Q50,20 50,50 Q50,20 20,20 Q50,50 50,90" />
                </svg>
            ),
            Frame: (props) => (
                <svg {...props} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect><circle cx="8.5" cy="8.5" r="1.5"></circle><polyline points="21 15 16 10 5 21"></polyline></svg>
            ),
            Move: (props) => (
                <svg {...props} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><polyline points="5 9 2 12 5 15"></polyline><polyline points="9 5 12 2 15 5"></polyline><polyline points="15 19 12 22 9 19"></polyline><polyline points="19 9 22 12 19 15"></polyline><line x1="2" y1="12" x2="22" y2="12"></line><line x1="12" y1="2" x2="12" y2="22"></line></svg>
            )
        };

        // --- Icônes ---
        const Icons = {
            ChevronLeft: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg>,
            ChevronRight: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><polyline points="9 18 15 12 9 6"></polyline></svg>,
            Settings: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><circle cx="12" cy="12" r="3"></circle><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 0 0 1-2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 0 0 1 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 0 0 1 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"></path></svg>,
            BarChart: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><line x1="18" y1="20" x2="18" y2="10"></line><line x1="12" y1="20" x2="12" y2="4"></line><line x1="6" y1="20" x2="6" y2="14"></line></svg>,
            X: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>,
            Save: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M19 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11l5 5v11a2 2 0 0 1-2 2z"></path><polyline points="17 21 17 13 7 13 7 21"></polyline><polyline points="7 3 7 8 15 8"></polyline></svg>,
            Trash2: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path><line x1="10" y1="11" x2="10" y2="17"></line><line x1="14" y1="11" x2="14" y2="17"></line></svg>,
            Edit3: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M12 20h9"></path><path d="M16.5 3.5a2.121 2.121 0 0 1 3 3L7 19l-4 1 1-4L16.5 3.5z"></path></svg>,
            Download: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path><polyline points="7 10 12 15 17 10"></polyline><line x1="12" y1="15" x2="12" y2="3"></line></svg>,
            Upload: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path><polyline points="17 8 12 3 7 8"></polyline><line x1="12" y1="3" x2="12" y2="15"></line></svg>,
            Lock: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"></rect><path d="M7 11V7a5 5 0 0 1 10 0v4"></path></svg>,
            Unlock: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"></rect><path d="M7 11V7a5 5 0 0 1 9.9-1"></path></svg>,
            Camera: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z"></path><circle cx="12" cy="13" r="4"></circle></svg>,
            FileText: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line><polyline points="10 9 9 9 8 9"></polyline></svg>,
            Pill: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M10.5 20.5a8 8 0 1 0 0-16 8 8 0 0 0 0 16z"></path><path d="M2 12h20"></path></svg>,
            Clock: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><circle cx="12" cy="12" r="10"></circle><polyline points="12 6 12 12 16 14"></polyline></svg>,
            Activity: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z" fill="currentColor" stroke="none"/></svg>,
            Edit: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path></svg>,
            Check: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="3" strokeLinecap="round" strokeLinejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>,
            Resize: (props) => <svg {...props} xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="3" strokeLinecap="round" strokeLinejoin="round"><polyline points="15 3 21 3 21 9"></polyline><polyline points="9 21 3 21 3 15"></polyline><line x1="21" y1="3" x2="14" y2="10"></line><line x1="3" y1="21" x2="10" y2="14"></line></svg>
        };

        const { useState, useEffect, useRef } = React;

        function MigraineTracker() {
            const [currentDate, setCurrentDate] = useState(new Date());
            const [selectedDate, setSelectedDate] = useState(null);
            const [logs, setLogs] = useState({}); 
            const [isModalOpen, setIsModalOpen] = useState(false);
            const [isStatsOpen, setIsStatsOpen] = useState(false);
            const [isSettingsOpen, setIsSettingsOpen] = useState(false);
            const [editingId, setEditingId] = useState(null);
            const [viewingEntry, setViewingEntry] = useState(null);
            const [appTitle, setAppTitle] = useState("Mon Journal");
            const [savedPin, setSavedPin] = useState(null);
            const [isLocked, setIsLocked] = useState(false);
            const [pinInput, setPinInput] = useState('');
            const [settingsPinInput, setSettingsPinInput] = useState('');
            
            // Gestion Images Multiples
            const [customImages, setCustomImages] = useState([]);
            const [isDecoMode, setIsDecoMode] = useState(false);
            const [draggingId, setDraggingId] = useState(null);
            const [resizingId, setResizingId] = useState(null);
            const dragOffset = useRef({ x: 0, y: 0 });

            const fileInputRef = useRef(null);
            const imageInputRef = useRef(null);

            const [formData, setFormData] = useState({
                type: 'Migraine', startTime: '', endTime: '', intensity: 5, symptoms: [], trigger: '', notes: '', medications: []
            });

            const commonSymptoms = ["Nausées", "Lumière", "Bruit", "Aura", "Vertiges", "Fatigue"];
            const medicationsList = ["Maxalt", "Imigrane", "Paracétamol", "Anti-inflam.", "Profenid"];

            useEffect(() => {
                const savedLogs = localStorage.getItem('migraineLogs');
                if (savedLogs) { try { setLogs(JSON.parse(savedLogs)); } catch(e) {} }
                const pin = localStorage.getItem('migrainePin');
                if (pin) { setSavedPin(pin); setIsLocked(true); }
                const title = localStorage.getItem('migraineAppTitle');
                if (title) setAppTitle(title);
                
                const savedImages = localStorage.getItem('migraineCustomImages');
                if (savedImages) {
                    try { setCustomImages(JSON.parse(savedImages)); } catch(e) {}
                }
            }, []);

            useEffect(() => {
                if (Object.keys(logs).length > 0) localStorage.setItem('migraineLogs', JSON.stringify(logs));
            }, [logs]);

            // Save images
            useEffect(() => {
                localStorage.setItem('migraineCustomImages', JSON.stringify(customImages));
            }, [customImages]);

            const handleUnlock = (e) => { e.preventDefault(); if (pinInput === savedPin) { setIsLocked(false); setPinInput(''); } else { alert("Code incorrect"); } };
            const handleSetPin = () => { if (settingsPinInput.length < 4) return alert("Code trop court"); localStorage.setItem('migrainePin', settingsPinInput); setSavedPin(settingsPinInput); setSettingsPinInput(''); alert("Code activé !"); };
            const handleRemovePin = () => { if (confirm("Supprimer le code PIN ?")) { localStorage.removeItem('migrainePin'); setSavedPin(null); } };
            const handleExportData = () => { const blob = new Blob([JSON.stringify(logs)], { type: "application/json" }); const url = URL.createObjectURL(blob); const link = document.createElement('a'); link.href = url; link.download = `backup_migraine.json`; link.click(); };
            const handleFileChange = (event) => { const file = event.target.files[0]; if (!file) return; const reader = new FileReader(); reader.onload = (e) => { try { const importedLogs = JSON.parse(e.target.result); if (confirm(`Importer ${Object.keys(importedLogs).length} entrées ?`)) { setLogs(importedLogs); alert("Succès !"); setIsSettingsOpen(false); } } catch (error) { alert("Fichier invalide."); } }; reader.readAsText(file); };
            const handleDownloadImage = () => { const element = document.getElementById('stats-content'); if (!element) return; html2canvas(element, { backgroundColor: "#FDFBF7", scale: 2 }).then(canvas => { const link = document.createElement('a'); link.download = `Bilan_${months[currentDate.getMonth()]}.png`; link.href = canvas.toDataURL("image/png"); link.click(); }); };
            
            // --- Gestion Images Multiples & Drag ---
            const handleCustomImageUpload = (event) => {
                const file = event.target.files[0];
                if (!file) return;
                if (file.size > 2 * 1024 * 1024) return alert("Image trop lourde (> 2Mo).");

                const reader = new FileReader();
                reader.onload = (e) => {
                    const newImage = {
                        id: Date.now(),
                        src: e.target.result,
                        x: 50 + Math.random() * 20,
                        y: 50 + Math.random() * 20,
                        width: 150 // Taille par défaut
                    };
                    setCustomImages(prev => [...prev, newImage]);
                    setIsDecoMode(true);
                    setIsSettingsOpen(false);
                };
                reader.readAsDataURL(file);
            };

            const removeImage = (id) => {
                setCustomImages(prev => prev.filter(img => img.id !== id));
            };

            const handlePointerDown = (e, imgId) => {
                if (!isDecoMode) return;
                e.stopPropagation();
                e.preventDefault();
                
                const clientX = e.touches ? e.touches[0].clientX : e.clientX;
                const clientY = e.touches ? e.touches[0].clientY : e.clientY;
                
                setDraggingId(imgId);
                const img = customImages.find(i => i.id === imgId);
                dragOffset.current = { x: clientX - img.x, y: clientY - img.y };
            };

            const handlePointerMove = (e) => {
                if (!isDecoMode) return;
                
                const clientX = e.touches ? e.touches[0].clientX : e.clientX;
                const clientY = e.touches ? e.touches[0].clientY : e.clientY;

                if (draggingId !== null) {
                    e.preventDefault();
                    setCustomImages(prev => prev.map(img => {
                        if (img.id === draggingId) {
                            return { ...img, x: clientX - dragOffset.current.x, y: clientY - dragOffset.current.y };
                        }
                        return img;
                    }));
                } else if (resizingId !== null) {
                    e.preventDefault();
                    setCustomImages(prev => prev.map(img => {
                        if (img.id === resizingId) {
                            // Calcul simple largeur: distance curseur - x de l'image
                            const newWidth = Math.max(50, clientX - img.x);
                            return { ...img, width: newWidth };
                        }
                        return img;
                    }));
                }
            };

            const handlePointerUp = () => {
                setDraggingId(null);
                setResizingId(null);
            };

            const months = ["Janvier", "Février", "Mars", "Avril", "Mai", "Juin", "Juillet", "Août", "Septembre", "Octobre", "Novembre", "Décembre"];
            const daysOfWeek = ["Lun", "Mar", "Mer", "Jeu", "Ven", "Sam", "Dim"];
            const getDaysInMonth = (year, month) => new Date(year, month + 1, 0).getDate();
            const getFirstDayOfMonth = (year, month) => { const day = new Date(year, month, 1).getDay(); return day === 0 ? 6 : day - 1; };
            const formatDateKey = (date) => `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, '0')}-${String(date.getDate()).padStart(2, '0')}`;
            const calculateDuration = (start, end) => { if (!start || !end) return ""; const [startH, startM] = start.split(':').map(Number); const [endH, endM] = end.split(':').map(Number); let diffM = (endH * 60 + endM) - (startH * 60 + startM); if (diffM < 0) diffM += 24 * 60; const hours = Math.floor(diffM / 60); const minutes = diffM % 60; return `${hours}h${minutes > 0 ? minutes : ''}`; };
            const getMonthlyStats = () => {
                const prefix = `${currentDate.getFullYear()}-${String(currentDate.getMonth() + 1).padStart(2, '0')}`;
                const monthlyLogs = Object.keys(logs).filter(k => k.startsWith(prefix)).flatMap(k => logs[k]).sort((a,b) => a.id - b.id);
                if (monthlyLogs.length === 0) return null;
                const total = monthlyLogs.length;
                const avg = (monthlyLogs.reduce((acc, c) => acc + c.intensity, 0) / total).toFixed(1);
                const symptomCounts = {}; monthlyLogs.forEach(l => l.symptoms.forEach(s => symptomCounts[s] = (symptomCounts[s] || 0) + 1));
                return { total, avg, monthlyLogs, migraines: monthlyLogs.filter(l => l.type === 'Migraine').length, cephalees: monthlyLogs.filter(l => l.type === 'Céphalée').length, topSymptoms: Object.entries(symptomCounts).sort((a,b) => b[1] - a[1]).slice(0, 3) };
            };

            const changeMonth = (i) => setCurrentDate(new Date(currentDate.getFullYear(), currentDate.getMonth() + i, 1));
            const handleDayClick = (day) => { const date = new Date(currentDate.getFullYear(), currentDate.getMonth(), day); setSelectedDate(date); setEditingId(null); setViewingEntry(null); setFormData({ type: 'Migraine', startTime: '', endTime: '', intensity: 5, symptoms: [], trigger: '', notes: '', medications: [] }); setIsModalOpen(true); };
            const handleViewEntry = (entry, dateObj) => setViewingEntry({ ...entry, dateObj });
            const handleEditFromView = () => { const entry = viewingEntry; setViewingEntry(null); setSelectedDate(entry.dateObj); setFormData(entry); setEditingId(entry.id); setIsModalOpen(true); };
            const handleSave = () => { if (!selectedDate) return; const dateKey = formatDateKey(selectedDate); const entryData = { ...formData, duration: calculateDuration(formData.startTime, formData.endTime) }; setLogs(prev => { let dayLogs = prev[dateKey] || []; if (editingId) dayLogs = dayLogs.map(e => e.id === editingId ? { ...e, ...entryData } : e); else dayLogs = [...dayLogs, { id: Date.now(), ...entryData }]; return { ...prev, [dateKey]: dayLogs }; }); setFormData({ type: 'Migraine', startTime: '', endTime: '', intensity: 5, symptoms: [], trigger: '', notes: '', medications: [] }); setEditingId(null); };
            const handleDelete = (id) => { const dateKey = formatDateKey(selectedDate); setLogs(prev => ({ ...prev, [dateKey]: prev[dateKey].filter(e => e.id !== id) })); if (editingId === id) setEditingId(null); };

            // UI Verrouillage
            if (isLocked) return ( <div className="min-h-screen bg-bg-paper flex flex-col items-center justify-center p-6 text-text-ink"><Art.TinyFlower className="w-32 h-32 text-terra-500 mb-8 opacity-100 drop-shadow-xl" /><h2 className="text-4xl font-serif mb-4 text-terra-600 font-bold">Bienvenue</h2><p className="text-sm font-bold opacity-80 mb-8 font-sans uppercase tracking-widest text-text-ink">Entrez votre code</p><form onSubmit={handleUnlock} className="w-full max-w-xs space-y-6"><input type="password" inputMode="numeric" pattern="[0-9]*" value={pinInput} onChange={(e) => setPinInput(e.target.value)} className="w-full text-center text-4xl tracking-[0.5em] p-4 bg-transparent border-b-4 border-terra-500 focus:outline-none placeholder-terra-100 text-terra-600 font-serif" placeholder="••••" maxLength="8" autoFocus /><button type="submit" className="w-full bg-terra-600 text-white py-4 rounded-full font-sans uppercase tracking-widest text-xs hover:bg-terra-500 transition shadow-xl font-bold">Ouvrir mon journal</button></form></div> );

            // UI Principale
            return (
                <div 
                    className="relative w-full h-full min-h-[85vh] bg-bg-paper shadow-2xl overflow-hidden flex flex-col font-sans fade-in border-[12px] border-terra-500 box-border rounded-xl"
                    onPointerMove={handlePointerMove}
                    onPointerUp={handlePointerUp}
                    onTouchMove={handlePointerMove}
                    onTouchEnd={handlePointerUp}
                >
                    
                    {/* Couche d'Images Personnalisées */}
                    <div className="absolute inset-0 overflow-hidden pointer-events-none z-0">
                        {customImages.map(img => (
                            <div 
                                key={img.id}
                                style={{ 
                                    transform: `translate(${img.x}px, ${img.y}px)`, 
                                    width: `${img.width || 150}px` 
                                }}
                                className={`absolute top-0 left-0 transition-shadow duration-200 ${isDecoMode ? 'pointer-events-auto' : 'pointer-events-none'}`}
                            >
                                <div 
                                    onPointerDown={(e) => handlePointerDown(e, img.id)}
                                    onTouchStart={(e) => handlePointerDown(e, img.id)}
                                    className={`w-full h-full ${isDecoMode ? 'cursor-move' : ''}`}
                                >
                                    <img src={img.src} alt="Déco" className={`w-full h-auto object-contain opacity-40 select-none ${isDecoMode ? 'border-2 border-dashed border-terra-600' : ''}`} draggable="false" />
                                </div>
                                {isDecoMode && (
                                    <>
                                        <button 
                                            onTouchEnd={(e) => { e.stopPropagation(); removeImage(img.id); }}
                                            onClick={(e) => { e.stopPropagation(); removeImage(img.id); }}
                                            className="absolute -top-3 -right-3 bg-red-500 text-white rounded-full p-1 shadow-lg z-50 pointer-events-auto"
                                        >
                                            <Icons.X className="w-4 h-4" />
                                        </button>
                                        <div 
                                            onPointerDown={(e) => { e.stopPropagation(); setResizingId(img.id); }}
                                            onTouchStart={(e) => { e.stopPropagation(); setResizingId(img.id); }}
                                            className="absolute bottom-0 right-0 p-1 bg-terra-600 rounded-tl-lg cursor-nwse-resize z-50 pointer-events-auto"
                                        >
                                            <Icons.Resize className="w-4 h-4 text-white" />
                                        </div>
                                    </>
                                )}
                            </div>
                        ))}
                        {/* Décorations par défaut si aucune image */}
                        {customImages.length === 0 && !isDecoMode && (
                            <>
                                <div className="absolute bottom-0 left-0 text-sage opacity-20 pointer-events-none rotate-12"><Art.LeafLine className="w-96 h-96 -translate-x-1/4 translate-y-1/4" /></div>
                                <div className="absolute top-32 left-8 text-terra-500 opacity-20 pointer-events-none"><Art.BirdLine className="w-32 h-32" /></div>
                            </>
                        )}
                    </div>

                    {/* Zone de contenu principale (estompée en mode déco) */}
                    <div className={`flex flex-col h-full z-10 transition-opacity duration-300 ${isDecoMode ? 'opacity-20 pointer-events-none' : 'opacity-100'}`}>
                        
                        {/* En-tête */}
                        <header className="p-6 pt-8 flex justify-between items-center relative">
                            <button onClick={() => setIsSettingsOpen(true)} className="text-text-ink hover:text-terra-600 transition p-2 bg-white/50 backdrop-blur rounded-full shadow-sm"><Icons.Settings className="w-6 h-6" /></button>
                            <div className="text-center relative flex items-center justify-center gap-2">
                                <h1 className="text-3xl md:text-4xl font-serif text-text-ink tracking-tight font-bold">{appTitle}</h1>
                                <Art.TinyFlower className="w-8 h-8 text-terra-500" />
                            </div>
                            <button onClick={() => setIsStatsOpen(true)} className="text-text-ink hover:text-terra-600 transition p-2 bg-white/50 backdrop-blur rounded-full shadow-sm"><Icons.BarChart className="w-6 h-6" /></button>
                        </header>

                        {/* Navigation */}
                        <div className="flex justify-between items-center px-6 py-6">
                            <button onClick={() => changeMonth(-1)} className="p-3 hover:bg-terra-100 rounded-full text-text-ink transition border-2 border-transparent hover:border-terra-100"><Icons.ChevronLeft /></button>
                            <span className="text-2xl font-serif text-terra-600 uppercase tracking-widest border-b-2 border-terra-100 pb-1 font-bold">{months[currentDate.getMonth()]} {currentDate.getFullYear()}</span>
                            <button onClick={() => changeMonth(1)} className="p-3 hover:bg-terra-100 rounded-full text-text-ink transition border-2 border-transparent hover:border-terra-100"><Icons.ChevronRight /></button>
                        </div>

                        {/* Calendrier */}
                        <div className="px-4 pb-20 flex-grow overflow-y-auto">
                            <div className="grid grid-cols-7 gap-2 mb-4">
                                {daysOfWeek.map(d => <div key={d} className="text-center text-xs uppercase font-black text-sage tracking-widest">{d}</div>)}
                            </div>
                            <div className="grid grid-cols-7 gap-2 md:gap-3">
                                {Array.from({ length: getFirstDayOfMonth(currentDate.getFullYear(), currentDate.getMonth()) }).map((_, i) => <div key={`empty-${i}`} />)}
                                {Array.from({ length: getDaysInMonth(currentDate.getFullYear(), currentDate.getMonth()) }, (_, i) => i + 1).map(day => {
                                    const dateKey = formatDateKey(new Date(currentDate.getFullYear(), currentDate.getMonth(), day));
                                    const dayLogs = logs[dateKey] || [];
                                    const isToday = new Date().toDateString() === new Date(currentDate.getFullYear(), currentDate.getMonth(), day).toDateString();
                                    
                                    return (
                                        <div key={day} onClick={() => handleDayClick(day)} 
                                            className={`min-h-[110px] md:min-h-[130px] rounded-2xl border-2 transition p-2 flex flex-col justify-between cursor-pointer relative group shadow-sm hover:shadow-lg hover:-translate-y-1
                                                ${isToday ? 'bg-white border-terra-500 ring-2 ring-terra-100' : 'bg-white/90 border-sand hover:border-terra-300'}`}>
                                            <span className={`text-lg font-serif ${isToday ? 'text-terra-600 font-bold' : 'text-text-ink font-semibold'}`}>{day}</span>
                                            <div className="flex flex-col gap-1">
                                                {dayLogs.map((l, idx) => (
                                                    <div key={idx} onClick={(e) => {e.stopPropagation(); handleViewEntry(l, new Date(currentDate.getFullYear(), currentDate.getMonth(), day))}}
                                                        className={`h-3 rounded-full w-full shadow-sm ${l.intensity > 7 ? 'bg-terra-600' : l.intensity > 4 ? 'bg-terra-500' : 'bg-sage'}`}></div>
                                                ))}
                                            </div>
                                        </div>
                                    );
                                })}
                            </div>
                            {/* Légende couleurs */}
                            <div className="mt-6 flex flex-wrap gap-4 text-xs text-text-ink justify-center font-bold uppercase tracking-wider">
                                <div className="flex items-center gap-1"><div className="w-3 h-3 bg-sage rounded-full"></div> Léger (1-4)</div>
                                <div className="flex items-center gap-1"><div className="w-3 h-3 bg-terra-500 rounded-full"></div> Modéré (5-7)</div>
                                <div className="flex items-center gap-1"><div className="w-3 h-3 bg-terra-600 rounded-full"></div> Sévère (8-10)</div>
                            </div>
                        </div>
                    </div>

                    {/* Barre d'outils Mode Déco (Si Actif) */}
                    {isDecoMode && (
                        <div className="absolute top-4 left-0 w-full flex justify-center z-50 pointer-events-auto">
                            <div className="bg-terra-600 text-white px-6 py-3 rounded-full shadow-2xl flex items-center gap-4 animate-fade-in">
                                <span className="font-bold text-sm uppercase tracking-wide">Mode Décoration</span>
                                <div className="h-4 w-px bg-white/30"></div>
                                <button onClick={() => imageInputRef.current.click()} className="hover:bg-terra-500 p-1 rounded"><Icons.Upload className="w-5 h-5"/></button>
                                <button onClick={() => setIsDecoMode(false)} className="bg-white text-terra-600 px-3 py-1 rounded-full text-xs font-bold hover:bg-gray-100"><Icons.Check className="w-4 h-4 inline mr-1"/>OK</button>
                            </div>
                        </div>
                    )}

                    {/* Modales (Design Contrasté) */}
                    {isSettingsOpen && (
                        <div className="fixed inset-0 z-50 flex items-end sm:items-center justify-center bg-text-ink/60 backdrop-blur-md">
                            <div className="bg-white w-full sm:w-96 rounded-t-3xl sm:rounded-3xl p-8 shadow-2xl animate-fade-in font-sans border-t-8 border-terra-500 max-h-[90vh] overflow-y-auto">
                                <div className="flex justify-between items-center mb-8">
                                    <h2 className="text-3xl font-serif text-text-ink font-bold">Paramètres</h2>
                                    <button onClick={() => setIsSettingsOpen(false)} className="p-2 hover:bg-gray-100 rounded-full"><Icons.X className="text-text-ink" /></button>
                                </div>
                                <div className="space-y-8">
                                    <div>
                                        <label className="text-xs uppercase tracking-wider text-sage font-black mb-2 block">Titre du journal</label>
                                        <input type="text" value={appTitle} onChange={(e) => {setAppTitle(e.target.value); localStorage.setItem('migraineAppTitle', e.target.value);}} 
                                            className="w-full bg-terra-100/30 border-2 border-sand p-4 rounded-xl text-text-ink focus:border-terra-500 outline-none font-serif text-xl font-bold" />
                                    </div>

                                    {/* Section Décoration */}
                                    <div className="border-t-2 border-sand pt-6">
                                        <label className="text-xs uppercase tracking-wider text-sage font-black mb-2 block flex items-center gap-2">
                                            <Art.Frame className="w-4 h-4" /> Décoration
                                        </label>
                                        
                                        <div className="grid grid-cols-2 gap-3">
                                            <input type="file" ref={imageInputRef} onChange={handleCustomImageUpload} className="hidden" accept="image/*" />
                                            <button onClick={() => imageInputRef.current.click()} className="bg-terra-100/50 border-2 border-dashed border-terra-500 rounded-xl p-4 flex flex-col items-center justify-center gap-2 hover:bg-terra-100 transition">
                                                <Icons.Camera className="w-6 h-6 text-terra-500" />
                                                <span className="text-[10px] font-bold text-terra-600 uppercase">Ajouter image</span>
                                            </button>
                                            
                                            <button onClick={() => { setIsDecoMode(true); setIsSettingsOpen(false); }} className="bg-sand border-2 border-sand rounded-xl p-4 flex flex-col items-center justify-center gap-2 hover:bg-gray-200 transition">
                                                <Art.Move className="w-6 h-6 text-text-ink" />
                                                <span className="text-[10px] font-bold text-text-ink uppercase">Organiser</span>
                                            </button>
                                        </div>
                                    </div>

                                    <div className="border-t-2 border-sand pt-6">
                                        <label className="text-xs uppercase tracking-wider text-sage font-black mb-2 block">Sécurité</label>
                                        {!savedPin ? (
                                            <div className="flex gap-2">
                                                <input type="text" inputMode="numeric" placeholder="PIN" value={settingsPinInput} onChange={(e) => setSettingsPinInput(e.target.value)} className="bg-terra-100/30 border-2 border-sand p-3 rounded-xl flex-grow outline-none font-bold text-center text-lg"/>
                                                <button onClick={handleSetPin} className="bg-terra-600 text-white px-6 rounded-xl text-xs uppercase font-bold hover:bg-terra-500 transition shadow-lg">Activer</button>
                                            </div>
                                        ) : (
                                            <button onClick={handleRemovePin} className="w-full py-4 border-2 border-red-200 text-red-500 rounded-xl text-xs uppercase font-bold hover:bg-red-50 transition">Désactiver le Code PIN</button>
                                        )}
                                    </div>
                                    <div className="border-t-2 border-sand pt-6 flex gap-3">
                                        <button onClick={handleExportData} className="flex-1 py-4 bg-sand text-text-ink rounded-xl text-xs uppercase font-bold hover:bg-gray-300 transition">Sauvegarder</button>
                                        <div className="relative flex-1">
                                            <input type="file" className="absolute inset-0 opacity-0 cursor-pointer" onChange={handleFileChange} />
                                            <button className="w-full h-full py-4 bg-sand text-text-ink rounded-xl text-xs uppercase font-bold hover:bg-gray-300 transition">Restaurer</button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    )}

                    {isStatsOpen && (
                        <div className="fixed inset-0 z-50 flex items-center justify-center bg-bg-paper/95 backdrop-blur-md p-4">
                            <div className="bg-white w-full max-w-lg h-[85vh] rounded-3xl shadow-2xl border-2 border-terra-100 flex flex-col overflow-hidden relative">
                                <button onClick={() => setIsStatsOpen(false)} className="absolute top-6 right-6 text-text-ink z-20 hover:text-terra-600 transition p-2 bg-white/80 rounded-full shadow-sm"><Icons.X /></button>
                                <div id="stats-content" className="p-8 overflow-y-auto flex-grow bg-white">
                                    <div className="text-center mb-10 pt-4">
                                        <Art.TinyFlower className="w-32 h-32 mx-auto text-text-ink mb-6" />
                                        <h2 className="text-3xl font-serif text-text-ink font-bold">{months[currentDate.getMonth()]} {currentDate.getFullYear()}</h2>
                                        <div className="h-2 w-24 bg-terra-500 mx-auto mt-4 rounded-full"></div>
                                    </div>
                                    
                                    {getMonthlyStats() ? (
                                        <div className="space-y-10">
                                            <div className="flex justify-center gap-8 bg-terra-100/30 p-6 rounded-3xl">
                                                <div className="text-center">
                                                    <span className="block text-5xl font-serif text-text-ink mb-1 font-bold">{getMonthlyStats().total}</span>
                                                    <span className="text-xs uppercase font-black text-terra-600 tracking-widest">Crises</span>
                                                </div>
                                                <div className="w-0.5 bg-terra-200"></div>
                                                <div className="text-center">
                                                    <span className="block text-5xl font-serif text-terra-600 mb-1 font-bold">{getMonthlyStats().avg}</span>
                                                    <span className="text-xs uppercase font-black text-terra-600 tracking-widest">Intensité</span>
                                                </div>
                                            </div>

                                            <div className="space-y-6">
                                                <h3 className="text-sm uppercase font-black text-text-ink tracking-widest border-b-4 border-terra-100 pb-3">Journal détaillé</h3>
                                                {getMonthlyStats().monthlyLogs.map((l, i) => (
                                                    <div key={i} className="flex gap-5 items-start group">
                                                        <div className={`mt-2 w-4 h-4 rounded-full flex-shrink-0 border-4 ${l.type === 'Migraine' ? 'bg-terra-500 border-terra-200' : 'bg-sage border-green-100'}`}></div>
                                                        <div className="flex-grow pb-6 border-b border-sand group-last:border-0">
                                                            <div className="flex justify-between items-baseline mb-2">
                                                                <span className="font-serif text-text-ink text-xl font-bold">{l.type}</span>
                                                                <span className="text-xs font-bold bg-text-ink text-white px-3 py-1 rounded-full">{l.intensity}/10</span>
                                                            </div>
                                                            <p className="text-sm text-gray-600 mb-2 font-serif font-medium">{l.duration}</p>
                                                            {l.medications?.length > 0 && <div className="flex flex-wrap gap-1 mt-2">
                                                                {l.medications.map(m => <span key={m} className="text-[10px] font-bold uppercase bg-terra-100 text-terra-600 px-2 py-1 rounded">{m}</span>)}
                                                            </div>}
                                                        </div>
                                                    </div>
                                                ))}
                                            </div>
                                        </div>
                                    ) : (
                                        <div className="text-center text-gray-400 py-20 font-serif italic text-lg">Le calme plat pour ce mois-ci.</div>
                                    )}
                                </div>
                                <div className="p-6 border-t border-terra-100 bg-white flex justify-center">
                                    <button onClick={handleDownloadImage} className="flex items-center gap-3 bg-text-ink text-white px-8 py-4 rounded-full text-xs uppercase font-bold tracking-widest hover:bg-terra-600 transition shadow-xl transform active:scale-95 border-2 border-transparent">
                                        <Icons.Camera className="w-5 h-5"/> Sauvegarder l'image
                                    </button>
                                </div>
                            </div>
                        </div>
                    )}

                    {isModalOpen && !viewingEntry && (
                        <div className="fixed inset-0 z-50 flex items-end sm:items-center justify-center bg-text-ink/60 backdrop-blur-sm">
                            <div className="bg-white w-full sm:w-[480px] h-[92vh] sm:h-auto sm:max-h-[90vh] rounded-t-[40px] sm:rounded-[40px] shadow-2xl flex flex-col animate-fade-in overflow-hidden relative">
                                <div className="absolute top-0 right-0 w-40 h-40 bg-terra-100 rounded-bl-[100px] -z-0 opacity-100">
                                    <Art.BirdLine className="w-24 h-24 absolute top-4 right-4 text-terra-500" />
                                </div>
                                <div className="p-8 pb-4 border-b border-transparent flex justify-between items-center z-10">
                                    <h2 className="font-serif text-3xl font-bold text-text-ink flex items-center gap-2">
                                        {selectedDate && `${selectedDate.getDate()} ${months[selectedDate.getMonth()]}`}
                                    </h2>
                                    <button onClick={() => setIsModalOpen(false)} className="bg-sand p-3 rounded-full hover:bg-terra-200 transition"><Icons.X className="text-text-ink w-6 h-6" /></button>
                                </div>
                                
                                <div className="p-8 pt-2 overflow-y-auto space-y-8 flex-grow z-10">
                                    <div className="flex bg-sand p-1.5 rounded-2xl">
                                        {['Migraine', 'Céphalée'].map(t => (
                                            <button key={t} onClick={() => setFormData({...formData, type: t})} 
                                                className={`flex-1 py-4 rounded-xl text-sm font-black uppercase tracking-wider transition ${formData.type === t ? 'bg-white shadow-lg text-terra-600 scale-100' : 'text-gray-400 scale-95'}`}>
                                                {t}
                                            </button>
                                        ))}
                                    </div>

                                    <div className="grid grid-cols-2 gap-6">
                                        {['startTime', 'endTime'].map(field => (
                                            <div key={field} className="space-y-2">
                                                <label className="text-xs uppercase font-black text-sage tracking-widest">{field === 'startTime' ? 'Début' : 'Fin'}</label>
                                                <input type="time" value={formData[field]} onChange={e => setFormData({...formData, [field]: e.target.value})} 
                                                    className="w-full bg-white border-2 border-sand p-4 rounded-2xl text-text-ink outline-none focus:border-terra-500 font-bold text-lg shadow-sm"/>
                                            </div>
                                        ))}
                                    </div>

                                    <div className="space-y-3 bg-terra-100/30 p-4 rounded-2xl border border-terra-100">
                                        <div className="flex justify-between items-end">
                                            <label className="text-xs uppercase font-black text-sage tracking-widest">Intensité</label>
                                            <span className="font-serif text-4xl font-bold text-terra-600">{formData.intensity}</span>
                                        </div>
                                        <input type="range" min="1" max="10" value={formData.intensity} onChange={e => setFormData({...formData, intensity: Number(e.target.value)})} className="w-full h-3 bg-white rounded-lg appearance-none cursor-pointer"/>
                                        <div className="flex justify-between text-[10px] text-terra-600 font-bold uppercase tracking-widest"><span>Gêne</span><span>Insoutenable</span></div>
                                    </div>

                                    <div>
                                        <label className="text-xs uppercase font-black text-sage tracking-widest mb-3 block">Symptômes</label>
                                        <div className="flex flex-wrap gap-3">
                                            {commonSymptoms.map(s => (
                                                <button key={s} onClick={() => setFormData(p => ({...p, symptoms: p.symptoms.includes(s) ? p.symptoms.filter(i => i!==s) : [...p.symptoms, s]}))}
                                                    className={`px-4 py-3 rounded-xl text-xs font-bold border-2 transition shadow-sm ${formData.symptoms.includes(s) ? 'bg-text-ink text-white border-text-ink' : 'bg-white border-sand text-gray-500'}`}>
                                                    {s}
                                                </button>
                                            ))}
                                        </div>
                                    </div>

                                    <div>
                                        <label className="text-xs uppercase font-black text-sage tracking-widest mb-3 block">Traitements</label>
                                        <div className="flex flex-wrap gap-3">
                                            {medicationsList.map(m => (
                                                <button key={m} onClick={() => setFormData(p => ({...p, medications: p.medications.includes(m) ? p.medications.filter(i => i!==m) : [...p.medications, m]}))}
                                                    className={`px-4 py-3 rounded-xl text-xs font-bold border-2 transition shadow-sm ${formData.medications.includes(m) ? 'bg-terra-500 text-white border-terra-500' : 'bg-white border-sand text-gray-500'}`}>
                                                    {m}
                                                </button>
                                            ))}
                                        </div>
                                    </div>

                                    <div className="space-y-4">
                                        <input type="text" placeholder="Déclencheur (ex: Stress, Règles)" value={formData.trigger} onChange={e => setFormData({...formData, trigger: e.target.value})} className="w-full bg-white border-2 border-sand p-4 rounded-2xl text-sm outline-none placeholder-gray-400 focus:border-terra-500 transition font-medium shadow-sm"/>
                                        <textarea placeholder="Notes personnelles..." rows="2" value={formData.notes} onChange={e => setFormData({...formData, notes: e.target.value})} className="w-full bg-white border-2 border-sand p-4 rounded-2xl text-sm outline-none placeholder-gray-400 focus:border-terra-500 transition font-medium shadow-sm"></textarea>
                                    </div>
                                </div>

                                <div className="p-8 border-t-2 border-sand bg-white z-10">
                                    <div className="flex gap-4">
                                        {editingId && <button onClick={resetForm} className="px-6 py-4 bg-sand rounded-2xl font-bold text-gray-500 uppercase text-xs tracking-widest hover:bg-gray-300">Annuler</button>}
                                        <button onClick={handleSave} className="flex-1 bg-terra-600 text-white py-4 rounded-2xl font-sans uppercase tracking-widest text-xs font-bold shadow-xl hover:bg-terra-500 transition transform active:scale-95">
                                            {editingId ? 'Mettre à jour' : 'Enregistrer la crise'}
                                        </button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    )}

                    {viewingEntry && (
                        <div className="fixed inset-0 z-50 flex items-center justify-center bg-text-ink/60 backdrop-blur-md p-6">
                            <div className="bg-white w-full max-w-sm rounded-[32px] shadow-2xl animate-fade-in relative overflow-hidden border-4 border-white ring-1 ring-sand">
                                <div className="absolute top-0 left-0 w-full h-32 bg-terra-100 -z-0">
                                    <Art.LeafLine className="w-48 h-48 absolute -top-10 -right-10 text-terra-500 opacity-30" />
                                </div>
                                <button onClick={() => setViewingEntry(null)} className="absolute top-4 right-4 z-50 p-3 bg-white/90 rounded-full shadow-sm hover:bg-white transition text-text-ink hover:text-terra-600"><Icons.X className="w-5 h-5"/></button>
                                
                                <div className="p-8 pt-12 relative z-10">
                                    <div className="mb-8">
                                        <span className={`inline-block px-4 py-1.5 rounded-full text-[10px] uppercase font-black tracking-widest text-white mb-3 shadow-md ${viewingEntry.type === 'Migraine' ? 'bg-terra-600' : 'bg-sage'}`}>
                                            {viewingEntry.type}
                                        </span>
                                        <h2 className="text-4xl font-serif text-text-ink leading-none font-bold">
                                            {viewingEntry.dateObj.getDate()} <span className="text-lg block text-gray-500 mt-1 font-sans font-medium">{months[viewingEntry.dateObj.getMonth()]}</span>
                                        </h2>
                                        <div className="flex items-center gap-2 mt-4 text-gray-600 text-sm font-bold">
                                            <Icons.Clock className="w-5 h-5 text-terra-500" />
                                            {viewingEntry.startTime} - {viewingEntry.endTime}
                                            <span className="font-normal text-gray-400">({viewingEntry.duration})</span>
                                        </div>
                                    </div>

                                    <div className="grid grid-cols-2 gap-4 mb-8">
                                        <div className="bg-bg-paper p-5 rounded-2xl border-2 border-sand">
                                            <p className="text-[10px] uppercase font-black text-sage mb-1 tracking-widest">Douleur</p>
                                            <p className="text-3xl font-serif text-text-ink font-bold">{viewingEntry.intensity}<span className="text-sm text-gray-400 font-sans font-normal">/10</span></p>
                                        </div>
                                        <div className="bg-bg-paper p-5 rounded-2xl border-2 border-sand">
                                            <p className="text-[10px] uppercase font-black text-sage mb-1 tracking-widest">Cause</p>
                                            <p className="text-lg font-serif text-text-ink truncate font-medium">{viewingEntry.trigger || '-'}</p>
                                        </div>
                                    </div>

                                    {(viewingEntry.medications?.length > 0) && (
                                        <div className="mb-6">
                                            <div className="flex flex-wrap gap-2">
                                                {viewingEntry.medications.map(m => <span key={m} className="bg-terra-100 text-terra-600 px-3 py-1.5 rounded-lg text-xs font-bold border border-terra-200">{m}</span>)}
                                            </div>
                                        </div>
                                    )}

                                    {viewingEntry.notes && (
                                        <div className="p-5 bg-sand/50 rounded-2xl italic text-sm text-gray-600 mb-8 border-l-4 border-terra-500 leading-relaxed font-serif">
                                            "{viewingEntry.notes}"
                                        </div>
                                    )}

                                    <div className="flex gap-4">
                                        <button onClick={(e) => {e.stopPropagation(); handleDelete(viewingEntry.id); setViewingEntry(null);}} className="p-4 text-red-400 hover:text-red-600 border-2 border-red-100 rounded-2xl transition hover:bg-red-50 hover:border-red-200"><Icons.Trash2 /></button>
                                        <button onClick={handleEditFromView} className="flex-1 bg-text-ink text-white rounded-2xl text-xs uppercase font-bold tracking-widest hover:bg-terra-600 transition shadow-lg">
                                            Modifier
                                        </button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    )}

                </div>
            );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<MigraineTracker />);
    </script>
</body>
</html>


