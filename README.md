import React, { useState, useEffect, useRef } from 'react';
import { 
  Volume2, PlayCircle, ArrowLeft, MessageCircle, Eye, EyeOff, 
  Utensils, Map, Users, Gamepad2, Mic, CheckCircle2, XCircle, 
  Loader2, Trophy, Star, Award, User, LogOut, Sparkles, School, BookOpen
} from 'lucide-react';

// ==========================================
// 1. ข้อมูลบทเรียน (Lessons Data)
// ==========================================
const LESSONS = [
  {
    id: 1,
    title: "Greeting & Introducing",
    thaiTitle: "การทักทายและแนะนำตัว",
    icon: Users,
    color: "bg-blue-500",
    dialogue: [
      { speaker: "A", text: "Hello! Are you a new student here?", thai: "สวัสดี! เธอเป็นนักเรียนใหม่ที่นี่ใช่ไหม?" },
      { speaker: "B", text: "Yes, I am. I just moved here.", thai: "ใช่แล้ว ฉันเพิ่งย้ายมาที่นี่" },
      { speaker: "A", text: "Welcome! My name is Alex. What is your name?", thai: "ยินดีต้อนรับ! ฉันชื่ออเล็กซ์ เธอชื่ออะไรเหรอ?" },
      { speaker: "B", text: "I'm Sarah. Nice to meet you, Alex.", thai: "ฉันชื่อซาร่า ยินดีที่ได้รู้จักนะอเล็กซ์" },
      { speaker: "A", text: "Nice to meet you too, Sarah. What class are you in?", thai: "ยินดีที่ได้รู้จักเช่นกันซาร่า เธออยู่ชั้นไหนเหรอ?" },
      { speaker: "B", text: "I am in Mathayom 2/1.", thai: "ฉันอยู่ ม.2/1 จ้ะ" },
      { speaker: "A", text: "Oh, really? Me too! Let's go to the classroom together.", thai: "โอ้ จริงเหรอ? ฉันก็เหมือนกัน! ไปห้องเรียนด้วยกันเถอะ" }
    ]
  },
  {
    id: 2,
    title: "At the Cafeteria",
    thaiTitle: "การสั่งอาหารในโรงอาหาร",
    icon: Utensils,
    color: "bg-orange-500",
    dialogue: [
      { speaker: "A", text: "I'm so hungry. What do you want to eat?", thai: "ฉันหิวมากเลย เธออยากกินอะไร?" },
      { speaker: "B", text: "I want to eat chicken rice. How about you?", thai: "ฉันอยากกินข้าวมันไก่ แล้วเธอหล่ะ?" },
      { speaker: "A", text: "I'll have a bowl of noodle soup.", thai: "ฉันจะกินก๋วยเตี๋ยวน้ำสักชาม" },
      { speaker: "B", text: "Do you want anything to drink?", thai: "เธออยากได้เครื่องดื่มอะไรไหม?" },
      { speaker: "A", text: "Yes, I would like a glass of iced tea.", thai: "ใช่ ขอชาเย็นสักแก้วนะ" },
      { speaker: "B", text: "Okay, let's find a table first.", thai: "โอเค งั้นเราไปหาโต๊ะนั่งกันก่อนเถอะ" }
    ]
  },
  {
    id: 3,
    title: "Asking for Directions",
    thaiTitle: "การถามทางในโรงเรียน",
    icon: Map,
    color: "bg-green-500",
    dialogue: [
      { speaker: "A", text: "Excuse me. Could you tell me where the library is?", thai: "ขอโทษนะคะ ช่วยบอกหน่อยได้ไหมว่าห้องสมุดอยู่ตรงไหน?" },
      { speaker: "B", text: "Sure! Go straight and turn left at the corner.", thai: "ได้เลยครับ! เดินตรงไปแล้วเลี้ยวซ้ายตรงหัวมุมนะ" },
      { speaker: "A", text: "Is it far from here?", thai: "มันอยู่ไกลจากตรงนี้ไหมคะ?" },
      { speaker: "B", text: "Not at all. It's next to the science laboratory.", thai: "ไม่ไกลเลยครับ มันอยู่ติดกับห้องทดลองวิทยาศาสตร์" },
      { speaker: "A", text: "Thank you very much for your help.", thai: "ขอบคุณมากนะคะที่ช่วยเหลือ" },
      { speaker: "B", text: "You're welcome. Have a good day!", thai: "ด้วยความยินดีครับ ขอให้เป็นวันที่ดีนะ!" }
    ]
  },
  {
    id: 4,
    title: "Hobbies & Free Time",
    thaiTitle: "งานอดิเรกและเวลาว่าง",
    icon: Gamepad2,
    color: "bg-purple-500",
    dialogue: [
      { speaker: "A", text: "What do you usually do in your free time?", thai: "ปกติเธอทำอะไรในเวลาว่างเหรอ?" },
      { speaker: "B", text: "I usually play video games and read comic books.", thai: "ฉันมักจะเล่นวิดีโอเกมและอ่านหนังสือการ์ตูน" },
      { speaker: "A", text: "That sounds fun! What kind of games do you like?", thai: "ฟังดูสนุกนะ! เธอชอบเกมแนวไหนล่ะ?" },
      { speaker: "B", text: "I like action and adventure games. What about you?", thai: "ฉันชอบเกมแอคชั่นและผจญภัย แล้วเธอหล่ะ?" },
      { speaker: "A", text: "I like playing football with my friends on weekends.", thai: "ฉันชอบเล่นฟุตบอลกับเพื่อนๆ ในวันหยุดสุดสัปดาห์น่ะ" },
      { speaker: "B", text: "Cool! Maybe I can join you this Saturday.", thai: "เจ๋งเลย! บางทีเสาร์นี้ฉันอาจจะไปร่วมเล่นด้วยนะ" },
      { speaker: "A", text: "That would be great! See you then.", thai: "ก็เยี่ยมไปเลย! แล้วเจอกันนะ" }
    ]
  }
];

// ==========================================
// 2. Main Application Component
// ==========================================
export default function App() {
  // Navigation & Profile State
  const [showWelcome, setShowWelcome] = useState(true);
  const [showLogoutConfirm, setShowLogoutConfirm] = useState(false);
  const [userProfile, setUserProfile] = useState(() => {
    try { const saved = localStorage.getItem('speakup_profile'); return saved ? JSON.parse(saved) : null; } 
    catch { return null; }
  });
  
  // Lesson State
  const [currentLesson, setCurrentLesson] = useState(null);
  const [showTranslation, setShowTranslation] = useState(true);
  const currentLessonRef = useRef(null);
  
  // Score State
  const [lessonScores, setLessonScores] = useState(() => {
    try { const saved = localStorage.getItem('speakup_scores'); return saved ? JSON.parse(saved) : {}; } 
    catch { return {}; }
  });
  const [completedLines, setCompletedLines] = useState(new Set());
  const [showCelebrate, setShowCelebrate] = useState(false);
  
  // Form State
  const [inputName, setInputName] = useState('');
  const [inputNumber, setInputNumber] = useState('');

  // Audio / Speech State
  const [isPlaying, setIsPlaying] = useState(false);
  const [playingIndex, setPlayingIndex] = useState(-1);
  const [listeningIndex, setListeningIndex] = useState(-1);
  const [speechFeedback, setSpeechFeedback] = useState({});
  const [isSpeechSupported, setIsSpeechSupported] = useState(true);
  
  const synthesisRef = useRef(null);

  useEffect(() => { currentLessonRef.current = currentLesson; }, [currentLesson]);

  useEffect(() => {
    if (userProfile) {
      localStorage.setItem('speakup_profile', JSON.stringify(userProfile));
      setShowWelcome(false);
    } else {
      localStorage.removeItem('speakup_profile');
    }
  }, [userProfile]);

  useEffect(() => {
    localStorage.setItem('speakup_scores', JSON.stringify(lessonScores));
  }, [lessonScores]);

  useEffect(() => {
    // Setup Speech APIs
    if (typeof window !== 'undefined') {
      synthesisRef.current = window.speechSynthesis;
      window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
      if (!window.SpeechRecognition) setIsSpeechSupported(false);
      
      // Load voices early
      if (window.speechSynthesis) {
        const loadVoices = () => window.speechSynthesis.getVoices();
        loadVoices();
        window.speechSynthesis.onvoiceschanged = loadVoices;
      }
    }
    return () => stopAllAudio();
  }, []);

  const stopAllAudio = () => {
    if (synthesisRef.current) synthesisRef.current.cancel();
    setIsPlaying(false);
    setPlayingIndex(-1);
  };

  const playAudio = (text, speaker, index = -1, onEndCallback = null) => {
    if (listeningIndex !== -1) return; // Prevent playing while mic is open
    stopAllAudio();
    if (!synthesisRef.current) return;

    try {
      const utterance = new SpeechSynthesisUtterance(text);
      utterance.lang = 'en-US';
      
      // Voice Selection Logic
      const voices = synthesisRef.current.getVoices();
      const englishVoices = voices.filter(v => v.lang.startsWith('en'));
      
      let selectedVoice = null;
      if (speaker === "A") {
        selectedVoice = englishVoices.find(v => (v.name.includes('Female') || v.name.includes('Samantha') || v.name.includes('Zira')) && !v.name.includes('Male'));
      } else {
        selectedVoice = englishVoices.find(v => v.name.includes('Male') || v.name.includes('Alex') || v.name.includes('David'));
      }
      if (!selectedVoice && englishVoices.length > 0) selectedVoice = englishVoices[0];
      if (selectedVoice) utterance.voice = selectedVoice;

      utterance.rate = 0.85; // Speak slightly slower for students
      utterance.pitch = speaker === "A" ? 1.1 : 0.95;
      
      utterance.onstart = () => { setIsPlaying(true); if (index !== -1) setPlayingIndex(index); };
      utterance.onend = () => { setIsPlaying(false); setPlayingIndex(-1); if (onEndCallback) onEndCallback(); };
      utterance.onerror = () => { setIsPlaying(false); setPlayingIndex(-1); };
      
      synthesisRef.current.speak(utterance);
    } catch (e) {
      console.warn("TTS Error:", e);
      setIsPlaying(false); setPlayingIndex(-1);
    }
  };

  const playFullDialogue = () => {
    if (isPlaying) { stopAllAudio(); return; }
    if (!currentLesson) return;

    let currentIndex = 0;
    const playNext = () => {
      if (currentIndex < currentLesson.dialogue.length && currentLessonRef.current === currentLesson) {
        const line = currentLesson.dialogue[currentIndex];
        playAudio(line.text, line.speaker, currentIndex, () => {
          currentIndex++;
          setTimeout(() => {
            if (currentIndex < currentLesson.dialogue.length && currentLessonRef.current === currentLesson) playNext();
          }, 500); 
        });
      }
    };
    playNext();
  };

  const startListening = (targetText, index) => {
    if (!isSpeechSupported || !window.SpeechRecognition) { 
        setSpeechFeedback(prev => ({ ...prev, [index]: { text: "เบราว์เซอร์ไม่รองรับไมค์ แนะนำให้ใช้ Google Chrome", isCorrect: false, isError: true } }));
        return; 
    }
    stopAllAudio();

    try {
        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        const recognition = new SpeechRecognition();
        recognition.lang = 'en-US'; 
        recognition.interimResults = false; 
        recognition.maxAlternatives = 1;

        recognition.onstart = () => { 
            setListeningIndex(index); 
            setSpeechFeedback(prev => ({ ...prev, [index]: null })); 
        };
        
        recognition.onresult = (event) => {
            const transcript = event.results[0][0].transcript;
            const normalize = t => t.toLowerCase().replace(/[^a-z0-9\s]/g, '').trim();
            const cleanTarget = normalize(targetText); 
            const cleanTranscript = normalize(transcript);

            // Flexible matching for students
            let isCorrect = cleanTranscript === cleanTarget || cleanTranscript.includes(cleanTarget) || cleanTarget.includes(cleanTranscript);
            
            // Give pass if they get at least a good chunk of it (simplification for M.2)
            if(!isCorrect && cleanTranscript.length > cleanTarget.length * 0.6) {
                // This is a simplified check, in a real app you might use Levenshtein distance
                isCorrect = true; 
            }

            setSpeechFeedback(prev => ({ ...prev, [index]: { text: transcript, isCorrect: isCorrect, target: targetText } }));

            if (isCorrect) {
                setCompletedLines(prev => {
                  const newSet = new Set(prev);
                  if (!newSet.has(index)) {
                      newSet.add(index);
                      setLessonScores(prevScores => ({ ...prevScores, [currentLesson.id]: (prevScores[currentLesson.id] || 0) + 1 }));
                      
                      if (newSet.size === currentLesson.dialogue.length) { 
                          setShowCelebrate(true); 
                          setTimeout(() => setShowCelebrate(false), 3500); 
                      }
                  }
                  return newSet;
                });
            }
        };
        
        recognition.onerror = (e) => {
            let msg = "ฟังเสียงไม่ชัด กรุณาลองใหม่";
            if (e.error === 'not-allowed') msg = "กรุณาอนุญาตให้เว็บใช้ไมโครโฟนก่อนนะครับ";
            setSpeechFeedback(prev => ({ ...prev, [index]: { text: msg, isCorrect: false, isError: true } }));
            setListeningIndex(-1);
        };
        
        recognition.onend = () => setListeningIndex(-1);
        recognition.start();
    } catch(err) {
        setSpeechFeedback(prev => ({ ...prev, [index]: { text: "เกิดข้อผิดพลาดในการเปิดไมค์", isCorrect: false, isError: true } }));
        setListeningIndex(-1);
    }
  };

  if (showWelcome && !userProfile) {
    return (
      <div className="min-h-screen bg-gradient-to-b from-blue-50 to-indigo-100 flex flex-col items-center justify-center p-6 font-sans relative overflow-hidden">
        <div className="absolute top-10 left-10 text-blue-200/50 rotate-12"><BookOpen size={120} /></div>
        <div className="absolute bottom-20 right-10 text-indigo-200/50 -rotate-12"><MessageCircle size={100} /></div>
        
        <div className="bg-white p-8 md:p-12 rounded-[2.5rem] shadow-2xl max-w-lg w-full text-center relative z-10 border border-white/50 backdrop-blur-sm">
          <div className="flex justify-center mb-6">
            <div className="relative">
              <div className="absolute inset-0 bg-blue-400 rounded-full blur-xl opacity-30 animate-pulse"></div>
              <div className="bg-gradient-to-tr from-blue-600 to-indigo-600 w-24 h-24 rounded-full flex items-center justify-center shadow-lg relative z-10">
                <Mic className="text-white" size={45} />
              </div>
            </div>
          </div>
          <h1 className="text-4xl md:text-5xl font-black text-transparent bg-clip-text bg-gradient-to-r from-blue-600 to-indigo-600 mb-2 tracking-tight">
            SpeakUp!
          </h1>
          <h2 className="text-xl md:text-2xl font-bold text-slate-700 mb-6">With Kru Kao ครูข้าว</h2>
          <div className="bg-blue-50 rounded-2xl p-5 mb-8 border border-blue-100">
            <p className="text-blue-800 font-semibold mb-2 flex items-center justify-center gap-2">
              <Sparkles size={18} className="text-yellow-500"/> แอปฝึกฟัง-พูด ภาษาอังกฤษ
            </p>
            <p className="text-slate-600 text-sm">สำหรับนักเรียนชั้นมัธยมศึกษาปีที่ 2</p>
            <div className="flex items-center justify-center gap-2 mt-3 pt-3 border-t border-blue-200/50 text-indigo-700 font-medium">
              <School size={18} /> โรงเรียนวัดบ้านสร้าง
            </div>
          </div>
          <button 
            onClick={() => setShowWelcome(false)}
            className="w-full bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-700 hover:to-indigo-700 text-white font-bold py-4 px-6 rounded-2xl shadow-xl transition-all flex justify-center items-center gap-3 text-lg"
          >
            เข้าสู่บทเรียน <PlayCircle size={22} className="animate-pulse"/>
          </button>
        </div>
      </div>
    );
  }

  if (!userProfile) {
    return (
      <div className="min-h-screen bg-slate-50 flex items-center justify-center p-4 font-sans bg-gradient-to-br from-blue-50 to-indigo-50">
        <div className="bg-white p-8 rounded-3xl shadow-xl max-w-md w-full border border-slate-100">
          <button onClick={() => setShowWelcome(true)} className="text-slate-400 hover:text-slate-600 mb-4 flex items-center gap-1 text-sm font-medium transition-colors">
            <ArrowLeft size={16} /> กลับหน้าแรก
          </button>
          <div className="text-center mb-8">
            <h2 className="text-2xl font-bold text-slate-800 mb-2">ยินดีต้อนรับนักเรียน! 👋</h2>
            <p className="text-slate-500 text-sm">กรุณากรอกชื่อและเลขที่ เพื่อให้ครูข้าวเก็บคะแนนให้ถูกคนนะคะ</p>
          </div>
          <form onSubmit={(e) => { 
              e.preventDefault(); 
              if (inputName && inputNumber) {
                  setUserProfile({ name: inputName, number: inputNumber }); 
              }
          }} className="space-y-5">
            <div>
              <label className="block text-sm font-bold text-slate-700 mb-2 ml-1">ชื่อ - นามสกุล</label>
              <input type="text" required placeholder="เช่น ด.ช. รักเรียน" value={inputName} onChange={(e) => setInputName(e.target.value)} className="w-full px-5 py-3.5 rounded-2xl border-2 border-slate-200 focus:border-blue-500 focus:outline-none bg-slate-50 focus:bg-white transition-all text-slate-800 font-medium"/>
            </div>
            <div>
              <label className="block text-sm font-bold text-slate-700 mb-2 ml-1">เลขที่</label>
              <input type="number" required min="1" placeholder="เช่น 1, 2, 3..." value={inputNumber} onChange={(e) => setInputNumber(e.target.value)} className="w-full px-5 py-3.5 rounded-2xl border-2 border-slate-200 focus:border-blue-500 focus:outline-none bg-slate-50 focus:bg-white transition-all text-slate-800 font-medium"/>
            </div>
            <button type="submit" className="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-4 px-4 rounded-2xl shadow-md mt-6 flex justify-center items-center gap-2 text-lg transition-colors">
              เริ่มฝึกพูดกันเลย! <Mic size={20}/>
            </button>
          </form>
        </div>
      </div>
    );
  }

  if (!currentLesson) {
    const totalScore = Object.values(lessonScores).reduce((acc, curr) => acc + curr, 0);
    const maxScore = LESSONS.reduce((acc, l) => acc + l.dialogue.length, 0);
    
    return (
      <div className="min-h-screen bg-slate-50 p-4 md:p-8 font-sans relative">
        {/* Logout Modal */}
        {showLogoutConfirm && (
          <div className="fixed inset-0 z-50 flex items-center justify-center bg-black/40 backdrop-blur-sm px-4">
            <div className="bg-white p-6 md:p-8 rounded-3xl shadow-2xl max-w-sm w-full text-center border border-slate-100">
              <div className="bg-red-100 w-16 h-16 rounded-full flex items-center justify-center mx-auto mb-4 text-red-500">
                  <LogOut size={32} />
              </div>
              <h3 className="text-xl font-bold text-slate-800 mb-2">ต้องการออกจากระบบ?</h3>
              <p className="text-slate-600 mb-6 text-sm">หากออกจากระบบ หรือเปลี่ยนชื่อผู้ใช้ คะแนนจะถูกรีเซ็ตใหม่ทั้งหมด</p>
              <div className="flex gap-3">
                <button onClick={() => setShowLogoutConfirm(false)} className="flex-1 py-3 bg-slate-100 text-slate-700 rounded-xl font-bold">ยกเลิก</button>
                <button onClick={() => { 
                    setUserProfile(null); setShowWelcome(true); setLessonScores({}); 
                    setInputName(''); setInputNumber(''); setShowLogoutConfirm(false); 
                }} className="flex-1 py-3 bg-red-500 text-white rounded-xl font-bold">ยืนยัน</button>
              </div>
            </div>
          </div>
        )}

        <div className="max-w-2xl mx-auto">
          <div className="flex justify-between items-center bg-white p-4 rounded-2xl shadow-sm mb-6 border border-slate-100">
            <div className="flex items-center gap-3">
              <div className="bg-indigo-100 p-2.5 rounded-full text-indigo-600"><User size={24} /></div>
              <div>
                  <p className="text-sm text-slate-500 font-medium">ยินดีต้อนรับ</p>
                  <p className="font-bold text-slate-800">{userProfile.name} <span className="text-indigo-500 ml-1">(เลขที่ {userProfile.number})</span></p>
              </div>
            </div>
            <button onClick={() => setShowLogoutConfirm(true)} className="text-slate-400 hover:text-red-500 p-2 rounded-xl bg-slate-50"><LogOut size={20} /></button>
          </div>
          
          <header className="mb-8 text-center">
            <h1 className="text-4xl font-black text-transparent bg-clip-text bg-gradient-to-r from-blue-600 to-indigo-600 mb-1">SpeakUp!</h1>
            <p className="text-slate-600 font-bold mb-3">With Kru Kao ครูข้าว</p>
            <div className="bg-gradient-to-r from-amber-400 to-orange-400 p-5 rounded-3xl shadow-md text-white flex items-center justify-between max-w-sm mx-auto mt-4">
              <div className="flex items-center gap-4">
                <div className="bg-white/20 p-3 rounded-full"><Trophy size={32} className="text-yellow-100" /></div>
                <div className="text-left"><p className="text-amber-100 text-sm font-medium mb-0.5">คะแนนสะสม</p><p className="text-3xl font-bold tracking-tight">{totalScore} <span className="text-xl text-amber-200 font-medium">/ {maxScore}</span></p></div>
              </div>
              <Award size={48} className="text-amber-200/40" />
            </div>
          </header>
          
          <div className="grid gap-4">
            {LESSONS.map((lesson) => {
              const Icon = lesson.icon; 
              const score = lessonScores[lesson.id] || 0; 
              const isDone = score === lesson.dialogue.length;
              return (
                <button 
                  key={lesson.id} 
                  onClick={() => {
                    setCurrentLesson(lesson);
                    stopAllAudio();
                    setSpeechFeedback({});
                    // Load completed lines for this lesson
                    const newCompleted = new Set();
                    for(let i = 0; i < score; i++) { if (i < lesson.dialogue.length) newCompleted.add(i); }
                    setCompletedLines(newCompleted);
                  }} 
                  className={`bg-white p-4 md:p-5 rounded-3xl shadow-sm border-2 transition-all text-left flex items-center gap-4 group relative overflow-hidden ${isDone ? 'border-amber-300 bg-amber-50/20' : 'border-transparent hover:border-blue-100'}`}
                >
                  <div className={`${isDone ? 'bg-amber-400' : lesson.color} p-4 rounded-2xl text-white shadow-sm z-10`}><Icon size={28} /></div>
                  <div className="flex-1 z-10">
                    <h2 className="text-xl font-bold text-slate-800 mb-1">{lesson.title}</h2>
                    <p className="text-slate-500 text-sm">{lesson.thaiTitle}</p>
                    <div className="mt-4 flex items-center gap-3">
                      <div className="flex-1 h-2.5 bg-slate-100 rounded-full overflow-hidden">
                        <div className={`h-full ${isDone ? 'bg-amber-400' : 'bg-blue-500'} transition-all`} style={{ width: `${(score / lesson.dialogue.length) * 100}%` }}/>
                      </div>
                      <span className="text-xs font-bold text-slate-500">{score}/{lesson.dialogue.length}</span>
                    </div>
                  </div>
                  <div className="w-12 h-12 rounded-full bg-slate-50 flex items-center justify-center text-slate-400 z-10"><ArrowLeft className="rotate-180" size={20} /></div>
                  {isDone && <div className="absolute -right-4 -top-4 opacity-10"><Trophy size={110} /></div>}
                </button>
              );
            })}
          </div>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-slate-50 flex flex-col font-sans relative">
      {showCelebrate && (
        <div className="fixed inset-0 z-50 flex items-center justify-center bg-black/40 backdrop-blur-sm px-4">
          <div className="bg-white p-8 rounded-3xl shadow-2xl text-center border-4 border-amber-200">
            <Trophy size={72} className="mx-auto text-amber-400 mb-4" />
            <h2 className="text-3xl font-bold text-slate-800 mb-2">ยอดเยี่ยมมาก!</h2>
            <p className="text-lg text-slate-600 font-medium">เก่งมากเลย {userProfile.name} 👏<br/>คุณผ่านบทนี้แล้ว</p>
          </div>
        </div>
      )}

      <header className="bg-white border-b border-slate-200 px-4 py-3 sticky top-0 z-10 shadow-sm">
        <div className="max-w-2xl mx-auto flex items-center justify-between">
          <div className="flex items-center gap-3">
            <button onClick={() => { stopAllAudio(); setCurrentLesson(null); setListeningIndex(-1); }} className="p-2 -ml-2 rounded-full bg-slate-50 text-slate-600"><ArrowLeft size={24} /></button>
            <div>
              <h1 className="font-bold text-slate-800 text-base md:text-lg leading-tight truncate max-w-[180px] md:max-w-xs">{currentLesson.title}</h1>
              <div className="flex items-center gap-1 mt-0.5">
                <Star size={12} className="text-amber-400 fill-amber-400" />
                <span className="text-xs font-medium text-amber-600">{completedLines.size}/{currentLesson.dialogue.length} คะแนน</span>
              </div>
            </div>
          </div>
          <div className="flex gap-2">
            <button onClick={() => setShowTranslation(!showTranslation)} className={`p-2 rounded-full flex items-center justify-center ${showTranslation ? 'bg-indigo-50 text-indigo-600' : 'bg-slate-100 text-slate-500'}`}>
              {showTranslation ? <Eye size={20} /> : <EyeOff size={20} />}
            </button>
          </div>
        </div>
      </header>

      <main className="flex-1 p-4 overflow-y-auto">
        <div className="max-w-2xl mx-auto space-y-6 pb-24">
          <div className="flex justify-center mb-6 mt-2">
            <button onClick={playFullDialogue} disabled={listeningIndex !== -1} className={`flex items-center gap-2 px-6 py-3.5 rounded-full font-bold shadow-sm ${isPlaying && playingIndex === -1 ? 'bg-red-50 text-red-600 border border-red-200' : listeningIndex !== -1 ? 'bg-slate-100 text-slate-400 opacity-50' : 'bg-white text-indigo-600 border border-indigo-200'}`}>
              {isPlaying && playingIndex === -1 ? (<>หยุดฟัง</>) : (<><PlayCircle size={22} />ฟังบทสนทนาทั้งหมด</>)}
            </button>
          </div>

          {!isSpeechSupported && (
            <div className="bg-red-50 text-red-700 p-4 rounded-xl text-sm font-medium mb-4 text-center border border-red-200">
              ⚠️ อุปกรณ์หรือเบราว์เซอร์นี้ไม่รองรับระบบไมโครโฟน <br/> (แนะนำให้ใช้ Google Chrome / Safari ตัวเต็ม)
            </div>
          )}

          {currentLesson.dialogue.map((line, index) => {
            const isA = line.speaker === "A"; 
            const isPlay = playingIndex === index; 
            const isListen = listeningIndex === index; 
            const feedback = speechFeedback[index]; 
            const isPass = completedLines.has(index);
            
            return (
              <div key={index} className={`flex w-full ${isA ? 'justify-end' : 'justify-start'}`}>
                {!isA && <div className="w-10 h-10 rounded-full bg-slate-300 flex-shrink-0 flex items-center justify-center text-white font-bold mr-3 mt-1 shadow-sm border border-slate-200">{line.speaker}</div>}
                
                <div className={`max-w-[85%] md:max-w-[75%] flex flex-col ${isA ? 'items-end' : 'items-start'}`}>
                  <div className={`relative p-4 md:p-5 rounded-2xl ${isA ? 'bg-blue-600 text-white rounded-tr-sm shadow-md' : 'bg-white text-slate-800 rounded-tl-sm border border-slate-200 shadow-sm'} ${isPlay ? 'ring-4 ring-indigo-300/50' : ''} ${isListen ? 'ring-4 ring-red-400/50' : ''} ${isPass && !isA ? 'border-amber-300 bg-amber-50/30' : ''} ${isPass && isA ? 'ring-2 ring-amber-300' : ''}`}>
                    
                    {isPass && <div className={`absolute -top-3 ${isA ? '-left-3' : '-right-3'} bg-white rounded-full p-1.5 shadow-md`}><Star size={16} className="text-amber-400 fill-amber-400" /></div>}
                    
                    <p className="text-[1.1rem] leading-relaxed mb-4">{line.text}</p>
                    
                    <div className={`flex flex-wrap items-center gap-2 pt-3 border-t ${isA ? 'border-blue-400' : 'border-slate-100'}`}>
                      <button onClick={() => playAudio(line.text, line.speaker, index)} className={`flex items-center gap-1.5 px-3.5 py-2 rounded-xl text-sm font-semibold ${isA ? 'bg-blue-500 text-white' : 'bg-slate-100 text-slate-700'}`}>
                        <Volume2 size={16} className={isPlay ? "animate-pulse" : ""} /> ฟัง
                      </button>
                      
                      <button onClick={() => startListening(line.text, index)} disabled={!isSpeechSupported} className={`flex items-center gap-1.5 px-3.5 py-2 rounded-xl text-sm font-semibold ${isListen ? 'bg-red-500 text-white animate-pulse' : isPass ? (isA ? 'bg-amber-500 text-white' : 'bg-amber-100 text-amber-700') : (isA ? 'bg-blue-500 text-white' : 'bg-slate-100 text-slate-700')} ${!isSpeechSupported ? 'opacity-50 cursor-not-allowed' : ''}`}>
                        {isListen ? <Loader2 size={16} className="animate-spin" /> : <Mic size={16} />}
                        {isListen ? 'กำลังฟัง...' : isPass ? 'พูดผ่านแล้ว' : 'ฝึกพูด'}
                      </button>
                    </div>
                  </div>
                  
                  {feedback && (
                    <div className={`mt-2 p-3 rounded-2xl text-sm w-full border shadow-sm ${feedback.isError ? 'bg-slate-50 border-slate-200 text-slate-600' : feedback.isCorrect ? 'bg-green-50 border-green-200 text-green-700' : 'bg-red-50 border-red-200 text-red-700'}`}>
                      <div className="flex items-start gap-2">
                        {feedback.isError ? null : feedback.isCorrect ? <CheckCircle2 size={18} className="shrink-0 mt-0.5 text-green-600"/> : <XCircle size={18} className="shrink-0 mt-0.5 text-red-600"/>}
                        <div>
                          <p className="font-bold mb-1">{feedback.isError ? 'ข้อผิดพลาด' : feedback.isCorrect ? 'ยอดเยี่ยม! รับ 1 คะแนน ⭐' : 'เกือบแล้ว ลองอีกครั้งนะ!'}</p>
                          {!feedback.isError && <p className="opacity-90">ระบบได้ยินว่า: <span className="italic">"{feedback.text}"</span></p>}
                          {feedback.isError && <p>{feedback.text}</p>}
                        </div>
                      </div>
                    </div>
                  )}

                  {showTranslation && <div className={`mt-2 text-sm font-medium ${isA ? 'text-right text-slate-500 pr-2' : 'text-left text-slate-500 pl-2'}`}>{line.thai}</div>}
                </div>
                {isA && <div className="w-10 h-10 rounded-full bg-blue-500 flex-shrink-0 flex items-center justify-center text-white font-bold ml-3 mt-1 shadow-sm border border-blue-400">{line.speaker}</div>}
              </div>
            );
          })}
        </div>
      </main>
      
      <div className="bg-white border-t border-slate-200 py-3.5 px-4 text-center text-sm font-medium text-slate-500 flex items-center justify-center gap-2 shadow-sm">
        <Sparkles size={16} className="text-indigo-500" /> ฟังเสียงแล้วกดฝึกพูด เพื่อสะสมดาวให้ครบทุกข้อนะ!
      </div>
    </div>
  );
}
