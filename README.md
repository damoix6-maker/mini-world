<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini World Creata - Скрипты</title>
<script src="https://cdn.tailwindcss.com"></script>
<style>
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:'Segoe UI',Arial,sans-serif;background:#0a0a0f;color:#e0e0e0;overflow-x:hidden;min-height:100vh}
::-webkit-scrollbar{width:6px}
::-webkit-scrollbar-track{background:#1a1a2e}
::-webkit-scrollbar-thumb{background:#6c5ce7;border-radius:3px}

@keyframes slideUp{from{opacity:0;transform:translateY(20px)}to{opacity:1;transform:translateY(0)}}
@keyframes pulseGlow{0%,100%{box-shadow:0 0 5px rgba(108,92,231,.3)}50%{box-shadow:0 0 20px rgba(108,92,231,.6)}}
@keyframes shimmer{0%{background-position:-200% center}100%{background-position:200% center}}
@keyframes celebrate{0%{transform:scale(.8);opacity:0}50%{transform:scale(1.05)}100%{transform:scale(1);opacity:1}}
@keyframes shake{0%,100%{transform:translateX(0)}25%{transform:translateX(-8px)}75%{transform:translateX(8px)}}
@keyframes fadeIn{from{opacity:0}to{opacity:1}}

.slide-up{animation:slideUp .4s ease forwards}
.fade-in{animation:fadeIn .3s ease forwards}

.glass{background:rgba(255,255,255,.03);backdrop-filter:blur(12px);border:1px solid rgba(255,255,255,.06)}
.glass-strong{background:rgba(255,255,255,.06);backdrop-filter:blur(20px);border:1px solid rgba(255,255,255,.1)}

.btn-primary{background:linear-gradient(135deg,#6c5ce7,#a855f7);transition:all .3s;position:relative;overflow:hidden;border:none;cursor:pointer}
.btn-primary:hover{transform:translateY(-2px);box-shadow:0 8px 25px rgba(108,92,231,.4)}
.btn-primary:active{transform:translateY(0)}
.btn-secondary{background:rgba(255,255,255,.05);border:1px solid rgba(255,255,255,.1);transition:all .3s;cursor:pointer}
.btn-secondary:hover{background:rgba(255,255,255,.1);border-color:rgba(108,92,231,.3)}
.btn-danger{background:rgba(239,68,68,.1);border:1px solid rgba(239,68,68,.2);color:#ef4444;transition:all .3s;cursor:pointer}
.btn-danger:hover{background:rgba(239,68,68,.2)}

.course-card{transition:all .4s;position:relative;overflow:hidden;cursor:pointer}
.course-card:hover{transform:translateY(-5px)}
.course-card:hover .course-bg{opacity:.15}
.course-bg{position:absolute;inset:0;background:linear-gradient(135deg,rgba(108,92,231,.1),rgba(168,85,247,.05));transition:opacity .4s;opacity:0}

.level-card,.practice-card{transition:all .4s cubic-bezier(.175,.885,.32,1.275);cursor:pointer;position:relative;overflow:hidden}
.level-card:hover:not(.locked){transform:scale(1.03);box-shadow:0 10px 30px rgba(108,92,231,.3)}
.level-card.locked{opacity:.4;cursor:not-allowed;filter:grayscale(.5)}
.level-card.completed{border-color:#00d26a}

.practice-card{border-left:3px solid #f59e0b}
.practice-card:hover{transform:scale(1.03);box-shadow:0 10px 30px rgba(245,158,11,.2)}
.practice-card.completed{border-left-color:#00d26a}

.particle-canvas{position:absolute;inset:0;pointer-events:none;z-index:0}

input,textarea,select{background:rgba(255,255,255,.05)!important;border:1px solid rgba(255,255,255,.1)!important;color:#e0e0e0!important;transition:all .3s;border-radius:.5rem}
input:focus,textarea:focus,select:focus{border-color:#6c5ce7!important;box-shadow:0 0 15px rgba(108,92,231,.2)!important;outline:none!important}

.code-editor{font-family:'Consolas','Monaco',monospace;background:#1a1a2e!important;border:1px solid rgba(108,92,231,.3)!important;color:#a8e6cf!important;line-height:1.6}

.progress-bar{background:linear-gradient(90deg,#6c5ce7,#a855f7);transition:width .6s}
.progress-amber{background:linear-gradient(90deg,#f59e0b,#ef4444)}
.progress-green{background:linear-gradient(90deg,#00d26a,#10b981)}

.shimmer-text{background:linear-gradient(90deg,#6c5ce7,#a855f7,#6c5ce7);background-size:200% auto;-webkit-background-clip:text;-webkit-text-fill-color:transparent;animation:shimmer 3s linear infinite}

.hero-gradient{background:radial-gradient(ellipse at 50% 0%,rgba(108,92,231,.15) 0%,transparent 60%),#0a0a0f}

.nav-link{position:relative;transition:color .3s}
.nav-link:hover{color:#a855f7}

.tab-btn{transition:all .2s;border-bottom:2px solid transparent;padding-bottom:6px}
.tab-btn.active{color:#a855f7;border-bottom-color:#a855f7}

.badge{display:inline-block;padding:2px 8px;border-radius:9999px;font-size:11px;font-weight:600}
.badge-purple{background:rgba(108,92,231,.15);color:#a78bfa;border:1px solid rgba(108,92,231,.3)}
.badge-amber{background:rgba(245,158,11,.15);color:#fbbf24;border:1px solid rgba(245,158,11,.3)}
.badge-green{background:rgba(0,210,106,.15);color:#00d26a;border:1px solid rgba(0,210,106,.3)}

.modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,.7);backdrop-filter:blur(8px);z-index:100;display:flex;align-items:center;justify-content:center;animation:fadeIn .2s ease}

.line-clamp-2{display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden}
.truncate{overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
</style>
</head>
<body>
<canvas id="fw-canvas" style="position:fixed;inset:0;z-index:9998;pointer-events:none"></canvas>
<div id="app"></div>
<script>
var S={
  page:'home',
  auth:false,
  tab:'create',
  adminSubTab:'levels',
  courses:[],
  progress:{},
  practiceProgress:{},
  curCourse:null,
  curLevel:0,
  curPractice:null,
  practiceRes:null,
  editCourse:null,
  editingPractice:null,
  editingLevel:null,
  editPracticeIdx:-1,
  editLevelIdx:-1
};

function save(){
  localStorage.setItem('mwc_c',JSON.stringify(S.courses));
  localStorage.setItem('mwc_p',JSON.stringify(S.progress));
  localStorage.setItem('mwc_pp',JSON.stringify(S.practiceProgress));
}
function load(){
  try{
    var c=localStorage.getItem('mwc_c');
    var p=localStorage.getItem('mwc_p');
    var pp=localStorage.getItem('mwc_pp');
    if(c)S.courses=JSON.parse(c);
    if(p)S.progress=JSON.parse(p);
    if(pp)S.practiceProgress=JSON.parse(pp);
  }catch(e){}
}

function initDefaults(){
  if(S.courses.length===0){
    S.courses=[
      {id:1,title:'Основы скриптов',description:'Изучите базовые команды скриптов в Mini World Creata',icon:'\u26A1',color:'#6c5ce7',locked:false,levels:[
        {id:1,title:'Переменные',content:'В Mini World Creata переменные хранят данные.\n\n\u2022 string — текст: "Привет"\n\u2022 number — числа: 42\n\u2022 boolean — true/false',task:'Создайте переменную и выведите её',practice:'let myVar = "Hello";\nprint(myVar);',keywords:['let','print']},
        {id:2,title:'Объекты',content:'Объекты — основа игры. Вы можете создавать и двигать их.\n\n\u2022 spawn — создать объект\n\u2022 move — переместить',task:'Создайте объект и переместите его',practice:'spawn("cube",x,y,z);\nmove(obj,10,5,0);',keywords:['spawn','move']}
      ],practices:[
        {id:10,title:'Создание мира',description:'Напишите скрипт для создания базового мира',task:'Создайте пол из 4 блоков и источник света',example:'spawn("grass",0,0,0);\nspawn("grass",2,0,0);\nspawn("grass",0,0,2);\nspawn("grass",2,0,2);\nspawn("torch",1,2,1);',keywords:['spawn']},
        {id:11,title:'Интерактивный объект',description:'Создайте объект, который реагирует на касание',task:'Создайте сундук, который спавнит алмаз при касании',example:'onPlayerTouch(function(){\n  spawn("diamond",x,y,z);\n  print("Вы нашли алмаз!");\n});',keywords:['onPlayerTouch','spawn','print']}
      ]},
      {id:2,title:'Продвинутые скрипты',description:'Функции, массивы и циклы для сложных механик',icon:'\uD83D\uDE80',color:'#a855f7',locked:true,levels:[
        {id:3,title:'Функции',content:'Функции позволяют переиспользовать код.\n\nfunction name() {\n  return value;\n}',task:'Создайте функцию',practice:'function spawnBlock(t){\n  return spawn(t,0,0,0);\n}',keywords:['function','return']},
        {id:4,title:'Циклы',content:'Циклы повторяют код.\n\u2022 for — со счётчиком',task:'Создайте цикл',practice:'for(let i=0;i<10;i++){\n  spawn("brick",i*2,0,0);\n}',keywords:['for','spawn']}
      ],practices:[
        {id:20,title:'Генератор стен',description:'Используйте функции и циклы для создания стен',task:'Напишите функцию, которая строит стену заданной длины',example:'function buildWall(len){\n  for(let i=0;i<len;i++){\n    spawn("stone",i*2,0,0);\n  }\n}\nbuildWall(5);',keywords:['function','for','spawn']}
      ]}
    ];
    save();
  }
}

function esc(s){var d=document.createElement('div');d.textContent=s||'';return d.innerHTML}

function render(){
  var app=document.getElementById('app');
  if(!app)return;
  if(S.page==='home')app.innerHTML=rHome();
  else if(S.page==='admin')app.innerHTML=rAdmin();
  else if(S.page==='course')app.innerHTML=rCourse();
  else if(S.page==='level')app.innerHTML=rLevel();
  else if(S.page==='levelPractice')app.innerHTML=rLevelPractice();
  else if(S.page==='coursePractice')app.innerHTML=rCoursePractice();
  else if(S.page==='complete')app.innerHTML=rComplete();
  setTimeout(initParticles,100);
}

// ===== HOME =====
function rHome(){
  var h='';
  for(var i=0;i<S.courses.length;i++){
    var c=S.courses[i];
    var pr=S.progress[c.id]||0;
    var ppr=getPracticeProgress(c.id);
    var done=pr>=c.levels.length;
    var lock=c.locked&&!done;
    var pct=Math.min(pr/c.levels.length*100,100);
    var ppct=ppr.total>0?Math.round(ppr.done/ppr.total*100):0;
    var btn=pr>0&&pr<c.levels.length?'Продолжить':'Начать';

    h+='<div class="course-card glass rounded-2xl p-5 slide-up" style="animation-delay:'+i*.1+'s" onclick="'+(lock?'':'goCourse('+c.id+')')+'">';
    h+='<div class="course-bg rounded-2xl"></div><div class="relative z-10">';
    h+='<div class="flex items-center gap-3 mb-3"><div class="w-12 h-12 rounded-xl flex items-center justify-center text-2xl flex-shrink-0" style="background:'+c.color+'22;border:1px solid '+c.color+'44">'+c.icon+'</div>';
    h+='<div class="min-w-0"><h3 class="text-lg font-bold text-white truncate">'+esc(c.title)+'</h3>';
    if(lock)h+='<span class="text-xs text-gray-500">\uD83D\uDD12 Заблокирован</span>';
    if(done)h+='<span class="text-xs text-green-400">\u2705 Пройден</span>';
    h+='</div></div>';
    h+='<p class="text-gray-400 text-xs mb-3 line-clamp-2">'+esc(c.description)+'</p>';
    h+='<div class="space-y-1.5 mb-3">';
    h+='<div class="flex items-center gap-2"><div class="flex-1 h-1.5 bg-white/5 rounded-full overflow-hidden"><div class="progress-bar h-full rounded-full" style="width:'+pct+'%"></div></div>';
    h+='<span class="text-xs text-gray-500 whitespace-nowrap">\u0423\u0440\u043E\u0432\u043D\u0438 '+pr+'/'+c.levels.length+'</span></div>';
    if(c.practices&&c.practices.length>0){
      h+='<div class="flex items-center gap-2"><div class="flex-1 h-1.5 bg-white/5 rounded-full overflow-hidden"><div class="progress-amber h-full rounded-full" style="width:'+ppct+'%"></div></div>';
      h+='<span class="text-xs text-gray-500 whitespace-nowrap">\u041F\u0440\u0430\u043A\u0442\u0438\u043A\u0438 '+ppr.done+'/'+ppr.total+'</span></div>';
    }
    h+='</div>';
    if(lock)h+='<button disabled class="w-full py-2 rounded-lg bg-white/5 text-gray-500 text-xs cursor-not-allowed">\uD83D\uDD12 Заблокирован</button>';
    else h+='<button onclick="event.stopPropagation();goCourse('+c.id+')" class="btn-primary w-full py-2 rounded-lg text-white text-xs font-semibold">'+btn+'</button>';
    h+='</div></div>';
  }

  var empty='';
  if(S.courses.length===0){
    empty='<div class="text-center py-16 slide-up"><div class="text-5xl mb-3">\uD83D\uDCDA</div><h3 class="text-lg text-gray-400 mb-2">Курсов пока нет</h3>';
    empty+='<button onclick="goAdminCreate()" class="btn-primary px-5 py-2 rounded-lg text-white text-sm font-semibold">Создать курс</button></div>';
  }

  return '<div class="min-h-screen hero-gradient pb-8">'+
    '<header class="glass-strong sticky top-0 z-50"><div class="max-w-5xl mx-auto px-4 py-3 flex items-center justify-between">'+
    '<div class="flex items-center gap-2"><div class="w-9 h-9 rounded-xl bg-gradient-to-br from-purple-500 to-violet-600 flex items-center justify-center text-lg" style="animation:pulseGlow 3s infinite">\uD83C\uDFAE</div>'+
    '<div><h1 class="text-base font-bold text-white leading-tight">Mini World Creata</h1><p class="text-[10px] text-gray-500">\u0418\u0437\u0443\u0447\u0435\u043D\u0438\u0435 \u0441\u043A\u0440\u0438\u043F\u0442\u043E\u0432</p></div></div>'+
    '<button onclick="goAdminCreate()" class="nav-link text-xs text-gray-300">\u2699\uFE0F \u0410\u0434\u043C\u0438\u043D</button></div></header>'+
    '<section class="max-w-5xl mx-auto px-4 pt-10">'+
    '<div class="text-center mb-8 slide-up"><h2 class="text-3xl md:text-4xl font-bold mb-2"><span class="shimmer-text">\u0418\u0437\u0443\u0447\u0430\u0439\u0442\u0435 \u0441\u043A\u0440\u0438\u043F\u0442\u044B</span></h2>'+
    '<p class="text-gray-400 text-sm max-w-xl mx-auto">\u041F\u0440\u043E\u0445\u043E\u0434\u0438\u0442\u0435 \u0443\u0440\u043E\u0432\u043D\u0438 \u0438 \u0432\u044B\u043F\u043E\u043B\u043D\u044F\u0439\u0442\u0435 \u043F\u0440\u0430\u043A\u0442\u0438\u0447\u0435\u0441\u043A\u0438\u0435 \u0437\u0430\u0434\u0430\u043D\u0438\u044F</p></div>'+
    '<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">'+h+'</div>'+empty+'</section></div>';
}

function goHome(){S.page='home';S.curCourse=null;render()}
function goAdminCreate(){S.page='admin';S.tab='create';S.auth=false;render()}
function goCourse(id){S.curCourse=id;S.curLevel=-1;S.curPractice=null;S.page='course';render()}

// ===== ADMIN =====
function rAdmin(){
  if(!S.auth)return '<div class="min-h-screen hero-gradient flex items-center justify-center">'+
    '<div class="glass-strong rounded-2xl p-8 max-w-sm mx-4 w-full slide-up text-center">'+
    '<div class="text-4xl mb-4">\uD83D\uDD10</div><h2 class="text-xl font-bold text-white mb-2">\u0410\u0434\u043C\u0438\u043D \u043F\u0430\u043D\u0435\u043B\u044C</h2>'+
    '<p class="text-gray-400 text-sm mb-5">\u0412\u0432\u0435\u0434\u0438\u0442\u0435 \u043F\u0430\u0440\u043E\u043B\u044C</p>'+
    '<input type="password" id="apass" placeholder="\u041F\u0430\u0440\u043E\u043B\u044C" class="w-full px-4 py-3 rounded-xl text-sm mb-3" onkeydown="if(event.key===\'Enter\')doLogin()">'+
    '<button onclick="doLogin()" class="btn-primary w-full py-3 rounded-xl text-white font-semibold">\u0412\u043E\u0439\u0442\u0438</button>'+
    '<button onclick="goHome()" class="mt-3 text-gray-400 text-sm hover:text-white">\u2190 \u041D\u0430\u0437\u0430\u0434</button></div></div>';

  var tabs=[
    {id:'create',label:'\u2728 \u0421\u043E\u0437\u0434\u0430\u0442\u044C'},
    {id:'list',label:'\uD83D\uDCCB \u0421\u043F\u0438\u0441\u043E\u043A'},
    {id:'practices',label:'\u270D\uFE0F \u041F\u0440\u0430\u043A\u0442\u0438\u043A\u0438'}
  ];

  var tabBtns='';
  for(var i=0;i<tabs.length;i++){
    var cls=S.tab===tabs[i].id?'active':'';
    tabBtns+='<button onclick="S.tab=\''+tabs[i].id+'\';render()" class="tab-btn text-sm '+cls+'">'+tabs[i].label+'</button>';
  }

  return '<div class="min-h-screen hero-gradient pb-8">'+
    '<header class="glass-strong sticky top-0 z-50"><div class="max-w-5xl mx-auto px-4 py-3 flex items-center justify-between">'+
    '<button onclick="goHome()" class="text-gray-400 hover:text-white text-sm">\u2190 \u041D\u0430\u0437\u0430\u0434</button>'+
    '<h2 class="text-base font-bold text-white">\u2699\uFE0F \u0410\u0434\u043C\u0438\u043D \u043F\u0430\u043D\u0435\u043B\u044C</h2><div class="w-16"></div></div></header>'+
    '<div class="max-w-5xl mx-auto px-4 py-6">'+
    '<div class="flex gap-4 mb-6 border-b border-white/5 pb-2">'+tabBtns+'</div>'+
    (S.tab==='create'?rCreate():S.tab==='list'?rList():rPracticesAdmin())+
    '</div></div>';
}

function doLogin(){
  var el=document.getElementById('apass');
  if(!el)return;
  if(el.value==='12345'){S.auth=true;render()}
  else{el.style.borderColor='#ef4444';el.style.animation='shake .5s';setTimeout(function(){el.style.borderColor='';el.style.animation=''},500)}
}

// ===== CREATE COURSE =====
function rCreate(){
  var c=S.editCourse||{title:'',description:'',icon:'\uD83D\uDCDA',color:'#6c5ce7',levels:[],practices:[]};
  var lvls='';
  for(var i=0;i<c.levels.length;i++)lvls+=rLevelEditor(c.levels[i],i);
  var pracs='';
  if(c.practices)for(var j=0;j<c.practices.length;j++)pracs+=rPracticeEditor(c.practices[j],j,true);

  return '<div class="glass-strong rounded-2xl p-5 slide-up">'+
    '<h2 class="text-xl font-bold text-white mb-4">\u2728 \u0421\u043E\u0437\u0434\u0430\u043D\u0438\u0435 \u043A\u0443\u0440\u0441\u0430</h2>'+
    '<div class="space-y-4">'+
    '<div class="grid grid-cols-1 sm:grid-cols-3 gap-3">'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u041D\u0430\u0437\u0432\u0430\u043D\u0438\u0435</label><input type="text" id="ctitle" class="w-full px-3 py-2 rounded-lg text-sm" value="'+esc(c.title)+'"></div>'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u0418\u043A\u043E\u043D\u043A\u0430</label><input type="text" id="cicon" class="w-full px-3 py-2 rounded-lg text-sm" value="'+esc(c.icon)+'"></div>'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u0426\u0432\u0435\u0442</label><input type="color" id="ccolor" class="w-full px-2 py-2 rounded-lg text-sm" style="height:36px;padding:2px" value="'+c.color+'"></div></div>'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u041E\u043F\u0438\u0441\u0430\u043D\u0438\u0435</label><textarea id="cdesc" rows="2" class="w-full px-3 py-2 rounded-lg text-sm resize-none">'+esc(c.description)+'</textarea></div>'+

    '<div class="border-t border-white/5 pt-3">'+
    '<div class="flex items-center justify-between mb-2"><h3 class="text-base font-semibold text-white">\uD83D\uDCDA \u0423\u0440\u043E\u0432\u043D\u0438 ('+c.levels.length+')</h3>'+
    '<button onclick="addLvl()" class="btn-primary px-3 py-1.5 rounded-lg text-xs text-white font-semibold">+ \u0423\u0440\u043E\u0432\u0435\u043D\u044C</button></div>'+
    '<div class="space-y-2 max-h-[300px] overflow-y-auto pr-1">'+lvls+'</div></div>'+

    '<div class="border-t border-white/5 pt-3">'+
    '<div class="flex items-center justify-between mb-2"><h3 class="text-base font-semibold text-white">\u270D\uFE0F \u041F\u0440\u0430\u043A\u0442\u0438\u043A\u0438 ('+((c.practices||[]).length)+')</h3>'+
    '<button onclick="addPrac()" class="btn-secondary px-3 py-1.5 rounded-lg text-xs text-amber-400 border-amber-500/30">+ \u041F\u0440\u0430\u043A\u0442\u0438\u043A\u0430</button></div>'+
    '<div class="space-y-2 max-h-[300px] overflow-y-auto pr-1">'+pracs+'</div></div>'+

    '<div class="flex gap-2 pt-2">'+
    '<button onclick="saveCourse()" class="btn-primary px-5 py-2 rounded-lg text-white text-sm font-semibold">\uD83D\uDCBE \u0421\u043E\u0445\u0440\u0430\u043D\u0438\u0442\u044C</button>'+
    '<button onclick="S.editCourse=null;render()" class="px-4 py-2 rounded-lg bg-white/5 text-gray-400 text-sm">\u0421\u0431\u0440\u043E\u0441</button></div></div></div>';
}

function rLevelEditor(lv,idx){
  return '<div class="glass rounded-xl p-3 slide-up" style="animation-delay:'+idx*.03+'s">'+
    '<div class="flex items-center justify-between mb-1"><h4 class="text-white font-semibold text-xs">\u0423\u0440\u043E\u0432\u0435\u043D\u044C '+(idx+1)+'</h4>'+
    '<button onclick="rmLvl('+idx+')" class="text-red-400 hover:text-red-300 text-xs">\u2716</button></div>'+
    '<div class="space-y-1">'+
    '<input type="text" id="lt'+idx+'" placeholder="\u041D\u0430\u0437\u0432\u0430\u043D\u0438\u0435" class="w-full px-2 py-1.5 rounded text-xs" value="'+esc(lv.title||'')+'">'+
    '<textarea id="lc'+idx+'" rows="2" placeholder="\u0421\u043E\u0434\u0435\u0440\u0436\u0430\u043D\u0438\u0435" class="w-full px-2 py-1.5 rounded text-xs resize-none">'+esc(lv.content||'')+'</textarea>'+
    '<input type="text" id="ltask'+idx+'" placeholder="\u0417\u0430\u0434\u0430\u043D\u0438\u0435" class="w-full px-2 py-1.5 rounded text-xs" value="'+esc(lv.task||'')+'">'+
    '<textarea id="lpr'+idx+'" rows="2" placeholder="\u041F\u0440\u0438\u043C\u0435\u0440 \u0440\u0435\u0448\u0435\u043D\u0438\u044F" class="w-full px-2 py-1.5 rounded text-xs resize-none code-editor">'+esc(lv.practice||'')+'</textarea>'+
    '<input type="text" id="lkw'+idx+'" placeholder="\u041A\u043B\u044E\u0447\u0435\u0432\u044B\u0435 \u0441\u043B\u043E\u0432\u0430 \u0447\u0435\u0440\u0435\u0437 \u0437\u0430\u043F\u044F\u0442\u0443\u044E" class="w-full px-2 py-1.5 rounded text-xs" value="'+esc((lv.keywords||[]).join(', '))+'">'+
    '</div></div>';
}

function rPracticeEditor(pc,idx,inline){
  return '<div class="glass rounded-xl p-3 slide-up practice-card" style="animation-delay:'+idx*.03+'s">'+
    '<div class="flex items-center justify-between mb-1"><h4 class="text-amber-400 font-semibold text-xs">\u270D\uFE0F '+esc(pc.title||'\u041D\u043E\u0432\u0430\u044F \u043F\u0440\u0430\u043A\u0442\u0438\u043A\u0430')+'</h4>'+
    '<button onclick="rmPrac('+idx+')" class="text-red-400 hover:text-red-300 text-xs">\u2716</button></div>'+
    '<div class="space-y-1">'+
    '<input type="text" id="pt'+idx+'" placeholder="\u041D\u0430\u0437\u0432\u0430\u043D\u0438\u0435 \u043F\u0440\u0430\u043A\u0442\u0438\u043A\u0438" class="w-full px-2 py-1.5 rounded text-xs" value="'+esc(pc.title||'')+'">'+
    '<input type="text" id="pdesc'+idx+'" placeholder="\u041E\u043F\u0438\u0441\u0430\u043D\u0438\u0435" class="w-full px-2 py-1.5 rounded text-xs" value="'+esc(pc.description||'')+'">'+
    '<input type="text" id="ptask'+idx+'" placeholder="\u0417\u0430\u0434\u0430\u043D\u0438\u0435" class="w-full px-2 py-1.5 rounded text-xs" value="'+esc(pc.task||'')+'">'+
    '<textarea id="pex'+idx+'" rows="3" placeholder="\u041F\u0440\u0438\u043C\u0435\u0440 \u0440\u0435\u0448\u0435\u043D\u0438\u044F" class="w-full px-2 py-1.5 rounded text-xs resize-none code-editor">'+esc(pc.example||'')+'</textarea>'+
    '<input type="text" id="pkw'+idx+'" placeholder="\u041A\u043B\u044E\u0447\u0435\u0432\u044B\u0435 \u0441\u043B\u043E\u0432\u0430 \u0447\u0435\u0440\u0435\u0437 \u0437\u0430\u043F\u044F\u0442\u0443\u044E" class="w-full px-2 py-1.5 rounded text-xs" value="'+esc((pc.keywords||[]).join(', '))+'">'+
    '</div></div>';
}

function addLvl(){if(!S.editCourse)S.editCourse={title:'',description:'',icon:'\uD83D\uDCDA',color:'#6c5ce7',levels:[],practices:[]};S.editCourse.levels.push({title:'',content:'',task:'',practice:'',keywords:[]});render()}
function rmLvl(i){S.editCourse.levels.splice(i,1);render()}
function addPrac(){
  if(!S.editCourse)S.editCourse={title:'',description:'',icon:'\uD83D\uDCDA',color:'#6c5ce7',levels:[],practices:[]};
  if(!S.editCourse.practices)S.editCourse.practices=[];
  S.editCourse.practices.push({title:'',description:'',task:'',example:'',keywords:[]});
  render();
}
function rmPrac(i){if(S.editCourse&&S.editCourse.practices)S.editCourse.practices.splice(i,1);render()}

function saveCourse(){
  var te=document.getElementById('ctitle');if(!te)return;
  var title=te.value.trim();
  var desc=document.getElementById('cdesc').value.trim();
  var icon=document.getElementById('cicon').value.trim()||'\uD83D\uDCDA';
  var color=document.getElementById('ccolor').value||'#6c5ce7';
  if(!title){alert('\u0412\u0432\u0435\u0434\u0438\u0442\u0435 \u043D\u0430\u0437\u0432\u0430\u043D\u0438\u0435');return}
  if(!desc){alert('\u0412\u0432\u0435\u0434\u0438\u0442\u0435 \u043E\u043F\u0438\u0441\u0430\u043D\u0438\u0435');return}

  var levels=[];
  var ec=S.editCourse;
  for(var i=0;i<ec.levels.length;i++){
    var t=document.getElementById('lt'+i);
    var cc=document.getElementById('lc'+i);
    var tk=document.getElementById('ltask'+i);
    var pp=document.getElementById('lpr'+i);
    var kw=document.getElementById('lkw'+i);
    var tt=t?t.value.trim():'';
    var co=cc?cc.value.trim():'';
    var ta=tk?tk.value.trim():'\u0412\u044B\u043F\u043E\u043B\u043D\u0438\u0442\u0435 \u0437\u0430\u0434\u0430\u043D\u0438\u0435';
    var pr=pp?pp.value.trim():'';
    var kws=kw?kw.value.split(',').map(function(s){return s.trim().toLowerCase()}).filter(function(s){return s}):[];
    if(tt&&co)levels.push({id:Date.now()+i,title:tt,content:co,task:ta,practice:pr,keywords:kws});
  }
  if(levels.length===0){alert('\u0414\u043E\u0431\u0430\u0432\u044C\u0442\u0435 \u0445\u043E\u0442\u044F \u0431\u044B \u043E\u0434\u0438\u043D \u0443\u0440\u043E\u0432\u0435\u043D\u044C');return}

  var practices=[];
  if(ec.practices){
    for(var j=0;j<ec.practices.length;j++){
      var pt=document.getElementById('pt'+j);
      var pd=document.getElementById('pdesc'+j);
      var ptk=document.getElementById('ptask'+j);
      var pex=document.getElementById('pex'+j);
      var pkw=document.getElementById('pkw'+j);
      var ptt=pt?pt.value.trim():'';
      var pdd=pd?pd.value.trim():'';
      var pttk=ptk?ptk.value.trim():'';
      var ppex=pex?pex.value.trim():'';
      var pkws=pkw?pkw.value.split(',').map(function(s){return s.trim().toLowerCase()}).filter(function(s){return s}):[];
      if(ptt)practices.push({id:Date.now()+100+j,title:ptt,description:pdd,task:pttk,example:ppex,keywords:pkws});
    }
  }

  var found=S.courses.findIndex(function(x){return ec&&x.id===ec.id});
  var nc={id:found>=0?ec.id:Date.now(),title:title,description:desc,icon:icon,color:color,locked:ec&&ec.id?ec.locked:false,levels:levels,practices:practices};

  if(found>=0)S.courses[found]=nc;else S.courses.push(nc);
  S.editCourse=null;save();render();
}

// ===== LIST =====
function rList(){
  if(S.courses.length===0)return '<div class="glass-strong rounded-2xl p-10 text-center slide-up"><div class="text-4xl mb-3">\uD83D\uDCEE</div><h3 class="text-gray-400">\u041D\u0435\u0442 \u043A\u0443\u0440\u0441\u043E\u0432</h3><button onclick="S.tab=\'create\';render()" class="btn-primary mt-3 px-5 py-2 rounded-lg text-white text-sm">\u0421\u043E\u0437\u0434\u0430\u0442\u044C</button></div>';
  var h='<div class="space-y-3">';
  for(var i=0;i<S.courses.length;i++){
    var c=S.courses[i];
    var pc=(c.practices||[]).length;
    h+='<div class="glass rounded-xl p-4 flex items-center gap-3 slide-up" style="animation-delay:'+i*.05+'s">'+
      '<div class="w-10 h-10 rounded-lg flex items-center justify-center text-xl flex-shrink-0" style="background:'+c.color+'22">'+c.icon+'</div>'+
      '<div class="min-w-0 flex-1"><h3 class="text-white font-semibold text-sm truncate">'+esc(c.title)+'</h3>'+
      '<div class="flex gap-2 mt-1"><span class="badge badge-purple">'+c.levels.length+' \u0443\u0440\u043E\u0432\u043D\u0435\u0439</span>'+
      '<span class="badge badge-amber">'+pc+' \u043F\u0440\u0430\u043A\u0442\u0438\u043A</span></div></div>'+
      '<div class="flex gap-1 flex-shrink-0">'+
      '<button onclick="editC('+c.id+')" class="px-3 py-1.5 rounded-lg bg-white/5 text-gray-400 text-xs hover:text-white">\u270F\uFE0F</button>'+
      '<button onclick="delC('+c.id+')" class="px-3 py-1.5 rounded-lg bg-red-500/10 text-red-400 text-xs hover:bg-red-500/20">\u2716</button></div></div>';
  }
  h+='</div>';return h;
}

function editC(id){
  var c=null;
  for(var i=0;i<S.courses.length;i++)if(S.courses[i].id===id)c=JSON.parse(JSON.stringify(S.courses[i]));
  if(c){S.editCourse=c;S.tab='create';render()}
}
function delC(id){if(confirm('\u0423\u0434\u0430\u043B\u0438\u0442\u044C \u043A\u0443\u0440\u0441?')){S.courses=S.courses.filter(function(x){return x.id!==id});save();render()}}

// ===== PRACTICES ADMIN =====
function rPracticesAdmin(){
  if(S.courses.length===0)return '<div class="glass-strong rounded-2xl p-10 text-center slide-up"><div class="text-4xl mb-3">\uD83D\uDCCE</div><h3 class="text-gray-400">\u0421\u043D\u0430\u0447\u0430\u043B\u0430 \u0441\u043E\u0437\u0434\u0430\u0439\u0442\u0435 \u043A\u0443\u0440\u0441</h3></div>';

  var h='<div class="space-y-4">';
  for(var i=0;i<S.courses.length;i++){
    var c=S.courses[i];
    var pracs=c.practices||[];
    h+='<div class="glass rounded-xl p-4 slide-up" style="animation-delay:'+i*.05+'s">'+
      '<div class="flex items-center gap-3 mb-3"><div class="w-9 h-9 rounded-lg flex items-center justify-center text-lg flex-shrink-0" style="background:'+c.color+'22">'+c.icon+'</div>'+
      '<div class="min-w-0 flex-1"><h3 class="text-white font-semibold text-sm">'+esc(c.title)+'</h3>'+
      '<p class="text-xs text-gray-400">'+pracs.length+' \u043F\u0440\u0430\u043A\u0442\u0438\u043A</p></div>'+
      '<button onclick="openPracModal('+c.id+')" class="btn-secondary px-3 py-1.5 rounded-lg text-xs text-amber-400">\u270D\uFE0F \u0414\u043E\u0431\u0430\u0432\u0438\u0442\u044C</button></div>';
    if(pracs.length>0){
      for(var j=0;j<pracs.length;j++){
        var p=pracs[j];
        h+='<div class="glass rounded-lg p-3 ml-12 mb-2 practice-card">'+
          '<div class="flex items-center justify-between">'+
          '<div class="min-w-0 flex-1"><h4 class="text-amber-400 font-semibold text-xs">'+esc(p.title)+'</h4>'+
          '<p class="text-gray-500 text-xs line-clamp-2">'+esc(p.description)+'</p></div>'+
          '<div class="flex gap-1 flex-shrink-0 ml-2">'+
          '<button onclick="openEditPracModal('+c.id+','+j+')" class="px-2 py-1 rounded bg-white/5 text-gray-400 text-xs hover:text-white">\u270F\uFE0F</button>'+
          '<button onclick="deletePrac('+c.id+','+j+')" class="px-2 py-1 rounded bg-red-500/10 text-red-400 text-xs">\u2716</button></div></div></div>';
      }
    } else {
      h+='<p class="text-gray-600 text-xs ml-12">\u041D\u0435\u0442 \u043F\u0440\u0430\u043A\u0442\u0438\u043A</p>';
    }
    h+='</div>';
  }
  h+='</div>';

  if(S.editingPractice!==null){
    h+=rEditPracticeModal();
  }

  return h;
}

function openPracModal(courseId){
  S.editingPractice={courseId:courseId,idx:-1,title:'',description:'',task:'',example:'',keywords:[]};
  render();
}

function openEditPracModal(courseId,idx){
  var course=getCourse(courseId);
  if(!course)return;
  var p=course.practices[idx];
  if(!p)return;
  S.editingPractice={courseId:courseId,idx:idx,title:p.title,description:p.description,task:p.task,example:p.example,keywords:p.keywords.slice()};
  render();
}

function rEditPracticeModal(){
  var ep=S.editingPractice;
  if(!ep)return'';
  var kwStr=(ep.keywords||[]).join(', ');
  return '<div class="modal-overlay" onclick="if(event.target===this){S.editingPractice=null;render()}">'+
    '<div class="glass-strong rounded-2xl p-5 max-w-lg w-full mx-4 slide-up" onclick="event.stopPropagation()">'+
    '<div class="flex items-center justify-between mb-4">'+
    '<h3 class="text-lg font-bold text-white">\u270D\uFE0F '+((ep.idx>=0)?'\u0420\u0435\u0434\u0430\u043A\u0442\u0438\u0440\u043E\u0432\u0430\u043D\u0438\u0435':'\u041D\u043E\u0432\u0430\u044F \u043F\u0440\u0430\u043A\u0442\u0438\u043A\u0430')+'</h3>'+
    '<button onclick="S.editingPractice=null;render()" class="text-gray-400 hover:text-white text-lg">\u00D7</button></div>'+
    '<div class="space-y-3">'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u041D\u0430\u0437\u0432\u0430\u043D\u0438\u0435</label><input type="text" id="mp-title" class="w-full px-3 py-2 rounded-lg text-sm" value="'+esc(ep.title)+'"></div>'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u041E\u043F\u0438\u0441\u0430\u043D\u0438\u0435</label><textarea id="mp-desc" rows="2" class="w-full px-3 py-2 rounded-lg text-sm resize-none">'+esc(ep.description)+'</textarea></div>'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u0417\u0430\u0434\u0430\u043D\u0438\u0435</label><input type="text" id="mp-task" class="w-full px-3 py-2 rounded-lg text-sm" value="'+esc(ep.task)+'"></div>'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u041F\u0440\u0438\u043C\u0435\u0440 \u0440\u0435\u0448\u0435\u043D\u0438\u044F</label><textarea id="mp-example" rows="4" class="w-full px-3 py-2 rounded-lg text-sm resize-none code-editor">'+esc(ep.example)+'</textarea></div>'+
    '<div><label class="text-xs text-gray-400 mb-1 block">\u041A\u043B\u044E\u0447\u0435\u0432\u044B\u0435 \u0441\u043B\u043E\u0432\u0430 (\u0447\u0435\u0440\u0435\u0437 \u0437\u0430\u043F\u044F\u0442\u0443\u044E)</label><input type="text" id="mp-kw" class="w-full px-3 py-2 rounded-lg text-sm" value="'+esc(kwStr)+'"></div>'+
    '<div class="flex gap-2 pt-2">'+
    '<button onclick="saveEditPractice()" class="btn-primary px-5 py-2 rounded-lg text-white text-sm font-semibold">\uD83D\uDCBE \u0421\u043E\u0445\u0440\u0430\u043D\u0438\u0442\u044C</button>'+
    '<button onclick="S.editingPractice=null;render()" class="px-4 py-2 rounded-lg bg-white/5 text-gray-400 text-sm">\u041E\u0442\u043C\u0435\u043D\u0430</button></div></div></div></div>';
}

function saveEditPractice(){
  var ep=S.editingPractice;
  if(!ep)return;
  var title=document.getElementById('mp-title').value.trim();
  var desc=document.getElementById('mp-desc').value.trim();
  var task=document.getElementById('mp-task').value.trim();
  var example=document.getElementById('mp-example').value.trim();
  var kwStr=document.getElementById('mp-kw').value;
  var keywords=kwStr.split(',').map(function(s){return s.trim().toLowerCase()}).filter(function(s){return s});

  if(!title){alert('\u0412\u0432\u0435\u0434\u0438\u0442\u0435 \u043D\u0430\u0437\u0432\u0430\u043D\u0438\u0435');return}

  var course=getCourse(ep.courseId);
  if(!course)return;
  if(!course.practices)course.practices=[];

  var prObj={
    id:ep.idx>=0?course.practices[ep.idx].id:Date.now(),
    title:title,
    description:desc,
    task:task,
    example:example,
    keywords:keywords
  };

  if(ep.idx>=0){
    course.practices[ep.idx]=prObj;
  }else{
    course.practices.push(prObj);
  }

  S.editingPractice=null;
  save();
  render();
}

function deletePrac(courseId,idx){
  if(!confirm('\u0423\u0434\u0430\u043B\u0438\u0442\u044C \u044D\u0442\u0443 \u043F\u0440\u0430\u043A\u0442\u0438\u043A\u0443?'))return;
  var course=getCourse(courseId);
  if(course&&course.practices){
    course.practices.splice(idx,1);
    save();
    render();
  }
}

function getCourse(id){
  for(var i=0;i<S.courses.length;i++)if(S.courses[i].id===id)return S.courses[i];
  return null;
}

// ===== COURSE VIEW =====
function rCourse(){
  var course=getCourse(S.curCourse);
  if(!course){goHome();return''}
  var pr=S.progress[course.id]||0;
  var ppr=getPracticeProgress(course.id);
  var done=pr>=course.levels.length;
  var allPracDone=ppr.total>0&&ppr.done>=ppr.total;

  var pct=Math.min(pr/course.levels.length*100,100);
  var ppct=ppr.total>0?Math.round(ppr.done/ppr.total*100):0;

  // Levels
  var lh='';
  for(var j=0;j<course.levels.length;j++){
    var lv=course.levels[j];
    var isDone=j<pr;
    var isCur=j===pr;
    var isLock=j>pr;
    var st=isDone?'completed':(isCur?'':'locked');
    var fn=isLock?'':'startLvl('+j+')';
    lh+='<div class="level-card glass rounded-xl p-4 '+st+'" style="cursor:'+(isLock?'not-allowed':'pointer')+';animation:slideUp .4s ease forwards;animation-delay:'+j*.08+'s" onclick="'+fn+'">'+
      '<canvas class="particle-canvas" id="pc'+j+'" data-status="'+(isDone?'completed':(isCur?'active':'locked'))+'" data-color="'+course.color+'"></canvas>'+
      '<div class="relative z-10 flex items-center gap-3">'+
      '<div class="w-9 h-9 rounded-lg flex items-center justify-center text-sm font-bold flex-shrink-0 '+
      (isDone?'bg-green-500/20 text-green-400':(isCur?'bg-purple-500/20 text-purple-400':'bg-white/5 text-gray-600'))+
      (isCur?' style="animation:pulseGlow 2s infinite"':'')+'">'+(isDone?'\u2713':(j+1))+'</div>'+
      '<div class="min-w-0">'+
      (isLock?'<h4 class="text-gray-600 font-semibold text-sm">\u0423\u0440\u043E\u0432\u0435\u043D\u044C '+(j+1)+'</h4>':'<h4 class="text-white font-semibold text-sm truncate">'+esc(lv.title)+'</h4>')+
      (isCur?'<p class="text-[10px] text-purple-400">\u25B6 \u0422\u0435\u043A\u0443\u0449\u0438\u0439</p>':'')+
      '</div>'+
      (isLock?'<span class="text-gray-600 ml-auto flex-shrink-0">\uD83D\uDD12</span>':'')+
      '</div></div>';
  }

  // Practices
  var ph='';
  var pracs=course.practices||[];
  if(pracs.length>0){
    for(var k=0;k<pracs.length;k++){
      var p=pracs[k];
      var pDone=isPracticeDone(course.id,p.id);
      ph+='<div class="practice-card glass rounded-xl p-4 '+((pDone?'completed':''))+'" onclick="startCoursePrac('+p.id+')" style="animation:slideUp .4s ease forwards;animation-delay:'+((course.levels.length+k)*.08)+'s">'+
        '<div class="flex items-center gap-3">'+
        '<div class="w-9 h-9 rounded-lg flex items-center justify-center text-sm font-bold flex-shrink-0 '+
        (pDone?'bg-green-500/20 text-green-400':'bg-amber-500/20 text-amber-400')+'">'+(pDone?'\u2713':'\u270D\uFE0F')+'</div>'+
        '<div class="min-w-0 flex-1">'+
        '<h4 class="text-white font-semibold text-sm truncate">'+esc(p.title)+'</h4>'+
        '<p class="text-xs text-gray-500 truncate">'+esc(p.description)+'</p></div>'+
        (pDone?'<span class="badge badge-green">\u0412\u044B\u043F\u043E\u043B\u043D\u0435\u043D\u043E</span>':'<span class="badge badge-amber">\u041D\u0435 \u0432\u044B\u043F\u043E\u043B\u043D\u0435\u043D\u043E</span>')+
        '</div></div>';
    }
  }

  return '<div class="min-h-screen hero-gradient pb-8">'+
    '<header class="glass-strong sticky top-0 z-50"><div class="max-w-4xl mx-auto px-4 py-3 flex items-center justify-between">'+
    '<button onclick="goHome()" class="text-gray-400 hover:text-white text-sm">\u2190 \u041A\u0443\u0440\u0441\u044B</button>'+
    '<h2 class="text-sm font-bold text-white">'+course.icon+' '+esc(course.title)+'</h2><div class="w-12"></div></div></header>'+
    '<div class="max-w-4xl mx-auto px-4 py-6">'+
    '<div class="glass-strong rounded-2xl p-5 mb-5 slide-up">'+
    '<div class="flex items-center gap-3 mb-3"><div class="w-12 h-12 rounded-xl flex items-center justify-center text-3xl flex-shrink-0" style="background:'+course.color+'22;border:1px solid '+course.color+'44">'+course.icon+'</div>'+
    '<div><h3 class="text-xl font-bold text-white">'+esc(course.title)+'</h3><p class="text-xs text-gray-400">'+esc(course.description)+'</p></div></div>'+
    '<div class="space-y-1.5">'+
    '<div class="flex items-center gap-2"><span class="text-xs text-purple-400 w-12">\u0423\u0440\u043E\u0432\u043D\u0438</span><div class="flex-1 h-1.5 bg-white/5 rounded-full overflow-hidden"><div class="progress-bar h-full rounded-full" style="width:'+pct+'%"></div></div><span class="text-xs text-gray-500">'+pr+'/'+course.levels.length+'</span></div>'+
    (pracs.length>0?'<div class="flex items-center gap-2"><span class="text-xs text-amber-400 w-12">\u041F\u0440\u0430\u043A\u0442</span><div class="flex-1 h-1.5 bg-white/5 rounded-full overflow-hidden"><div class="progress-amber h-full rounded-full" style="width:'+ppct+'%"></div></div><span class="text-xs text-gray-500">'+ppr.done+'/'+ppr.total+'</span></div>':'')+
    '</div></div>'+
    '<h3 class="text-base font-bold text-white mb-2">\uD83D\uDCDA \u0423\u0440\u043E\u0432\u043D\u0438</h3>'+
    '<div class="space-y-2 mb-6">'+lh+'</div>'+
    (pracs.length>0?'<h3 class="text-base font-bold text-white mb-2">\u270D\uFE0F \u041F\u0440\u0430\u043A\u0442\u0438\u043A\u0438</h3><div class="space-y-2">'+ph+'</div>':'')+
    '</div></div>';
}

// ===== LEVEL VIEW =====
function rLevel(){
  var course=getCourse(S.curCourse);
  if(!course)return'';
  var lv=course.levels[S.curLevel];
  if(!lv)return'';

  var ch='';
  var lines=lv.content.split('\n');
  for(var i=0;i<lines.length;i++){
    var line=lines[i];
    if(line.trim()==='')ch+='<br>';
    else if(line.trim().indexOf('\u2022')===0)ch+='<li class="text-gray-300 ml-4 text-sm">'+esc(line.trim().slice(1))+'</li>';
    else ch+='<p class="text-gray-300 text-sm">'+esc(line)+'</p>';
  }

  return '<div class="min-h-screen hero-gradient pb-8">'+
    '<header class="glass-strong sticky top-0 z-50"><div class="max-w-4xl mx-auto px-4 py-3 flex items-center justify-between">'+
    '<button onclick="S.page=\'course\';S.curLevel=-1;render()" class="text-gray-400 hover:text-white text-sm">\u2190 \u0423\u0440\u043E\u0432\u043D\u0438</button>'+
    '<h2 class="text-xs font-bold text-gray-300">'+esc(course.title)+' \u2014 \u0423\u0440. '+(S.curLevel+1)+'</h2><div class="w-12"></div></div></header>'+
    '<div class="max-w-4xl mx-auto px-4 py-6">'+
    '<div class="glass-strong rounded-2xl p-6 slide-up">'+
    '<div class="flex items-center gap-3 mb-4"><div class="w-9 h-9 rounded-lg bg-purple-500/20 flex items-center justify-center text-purple-400 font-bold text-sm">'+(S.curLevel+1)+'</div>'+
    '<h3 class="text-lg font-bold text-white">'+esc(lv.title)+'</h3></div>'+
    '<div class="text-gray-300 space-y-2 mb-6 leading-relaxed">'+ch+'</div>'+
    '<div class="border-t border-white/5 pt-4">'+
    '<h4 class="text-base font-semibold text-white mb-2">\uD83D\uDCDD \u0417\u0430\u0434\u0430\u0447\u0430</h4>'+
    '<p class="text-gray-400 text-sm mb-4">'+esc(lv.task)+'</p>'+
    '<div class="flex gap-2">'+
    '<button onclick="startLvlPrac()" class="btn-primary px-5 py-2 rounded-lg text-white text-sm font-semibold">\uD83D\uDE80 \u041F\u0440\u0430\u043A\u0442\u0438\u043A\u0430</button>'+
    '<button onclick="S.page=\'course\';render()" class="px-4 py-2 rounded-lg bg-white/5 text-gray-400 text-sm">\u041F\u0440\u043E\u043F\u0443\u0441\u0442\u0438\u0442\u044C</button></div></div></div></div></div>';
}

function rLevelPractice(){
  var course=getCourse(S.curCourse);
  if(!course)return'';
  var lv=course.levels[S.curLevel];
  if(!lv)return'';
  return rPracticePage(lv.practice,lv.keywords,lv.task,'level');
}

function rCoursePractice(){
  var course=getCourse(S.curCourse);
  if(!course)return'';
  var p=null;
  for(var i=0;i<(course.practices||[]).length;i++)if(course.practices[i].id===S.curPractice){p=course.practices[i];break}
  if(!p)return'';
  return rPracticePage(p.example,p.keywords,p.task,'practice',p.id);
}

function rPracticePage(exampleCode,keywords,task,type,practiceId){
  var r=S.practiceRes;
  var rh='';
  if(r){
    var cls=r.success?'bg-green-500/10 border border-green-500/30':'bg-red-500/10 border border-red-500/30';
    var icon=r.success?'\uD83C\uDF89':'\u274C';
    var col=r.success?'text-green-400':'text-red-400';
    var msg=r.success?'\u041E\u0442\u043B\u0438\u0447\u043D\u043E! \u041A\u043E\u0434 \u043F\u0440\u0438\u043D\u044F\u0442!':'\u041F\u043E\u043F\u0440\u043E\u0431\u0443\u0439\u0442\u0435 \u0435\u0449\u0451 \u0440\u0430\u0437';
    rh='<div class="rounded-xl p-3 mb-4 '+cls+'"><div class="flex items-center gap-2"><span class="text-lg">'+icon+'</span><span class="font-semibold '+col+' text-sm">'+msg+'</span></div>';
    if(!r.success&&r.detail)rh+='<p class="text-xs text-red-300 mt-2">'+r.detail+'</p>';
    rh+='</div>';
  }

  var nb='';
  if(r&&r.success){
    if(type==='level')nb='<button onclick="completeLvl()" class="px-5 py-2 rounded-lg bg-green-500/20 text-green-400 hover:bg-green-500/30 transition-colors font-semibold text-sm">\u0414\u0430\u043B\u0435\u0435 \u2192</button>';
    else nb='<button onclick="completeCoursePrac()" class="px-5 py-2 rounded-lg bg-green-500/20 text-green-400 hover:bg-green-500/30 transition-colors font-semibold text-sm">\u0417\u0430\u0432\u0435\u0440\u0448\u0438\u0442\u044C \u2192</button>';
  }

  var backFn=type==='level'?'S.page=\'level\';S.practiceRes=null;render()':'S.page=\'course\';S.practiceRes=null;S.curPractice=null;render()';

  return '<div class="min-h-screen hero-gradient pb-8">'+
    '<header class="glass-strong sticky top-0 z-50"><div class="max-w-4xl mx-auto px-4 py-3 flex items-center justify-between">'+
    '<button onclick="'+backFn+'" class="text-gray-400 hover:text-white text-sm">\u2190 \u041D\u0430\u0437\u0430\u0434</button>'+
    '<h2 class="text-xs font-bold text-gray-300">\u041F\u0440\u0430\u043A\u0442\u0438\u043A\u0430</h2><div class="w-12"></div></div></header>'+
    '<div class="max-w-4xl mx-auto px-4 py-6">'+
    '<div class="glass-strong rounded-2xl p-6 slide-up">'+
    '<div class="flex items-center gap-3 mb-4"><div class="w-9 h-9 rounded-lg bg-amber-500/20 flex items-center justify-center text-amber-400">\u270D\uFE0F</div>'+
    '<h3 class="text-lg font-bold text-white">\u041F\u0440\u0430\u043A\u0442\u0438\u043A\u0430</h3></div>'+
    '<p class="text-gray-400 text-sm mb-3">'+esc(task)+'</p>'+
    '<div class="mb-4"><label class="text-xs text-gray-400 mb-1 block">\u041F\u0440\u0438\u043C\u0435\u0440 \u0440\u0435\u0448\u0435\u043D\u0438\u044F:</label>'+
    '<div class="code-editor px-3 py-2 rounded-lg text-sm whitespace-pre-wrap">'+esc(exampleCode||'\u041F\u0440\u0438\u043C\u0435\u0440 \u043D\u0435 \u0443\u043A\u0430\u0437\u0430\u043D')+'</div></div>'+
    '<div class="mb-4"><label class="text-xs text-gray-400 mb-1 block">\u0412\u0430\u0448 \u043A\u043E\u0434:</label>'+
    '<textarea id="pcode" rows="5" placeholder="\u041D\u0430\u043F\u0438\u0448\u0438\u0442\u0435 \u0441\u043A\u0440\u0438\u043F\u0442..." class="w-full px-3 py-2 rounded-lg text-sm resize-none code-editor">'+(r?esc(r.code):'')+'</textarea></div>'+
    rh+
    '<div class="flex gap-2">'+
    '<button onclick="checkPracCode(\''+type+'\',\''+(practiceId||'')+'\')" class="btn-primary px-5 py-2 rounded-lg text-white text-sm font-semibold">\u2705 \u041F\u0440\u043E\u0432\u0435\u0440\u0438\u0442\u044C</button>'+
    nb+'</div></div></div></div>';
}

function startLvlPrac(){S.page='levelPractice';S.practiceRes=null;render()}
function startCoursePrac(id){S.curPractice=id;S.page='coursePractice';S.practiceRes=null;render()}

function checkPracCode(type,practiceId){
  var el=document.getElementById('pcode');
  if(!el)return;
  var code=el.value.trim();
  if(!code){alert('\u041D\u0430\u043F\u0438\u0448\u0438\u0442\u0435 \u043A\u043E\u0434!');return}

  var course=getCourse(S.curCourse);
  if(!course)return;

  var exampleCode='';
  var kws=[];
  var task='';

  if(type==='level'){
    var lv=course.levels[S.curLevel];
    if(lv){exampleCode=lv.practice;kws=lv.keywords;task=lv.task;}
  }else{
    for(var i=0;i<(course.practices||[]).length;i++){
      if(course.practices[i].id==practiceId){
        exampleCode=course.practices[i].example;
        kws=course.practices[i].keywords;
        task=course.practices[i].task;
        break;
      }
    }
  }

  var result=checkCode(code,exampleCode,kws);
  S.practiceRes={code:code,success:result.ok,detail:result.msg};
  render();
}

function checkCode(userCode,correctCode,keywords){
  function normalize(code){
    return code.replace(/[ \t]+$/gm,'').replace(/\n{3,}/g,'\n\n').trim().toLowerCase();
  }
  var normUser=normalize(userCode);
  var normCorrect=normalize(correctCode);

  if(normUser===normCorrect)return{ok:true,msg:'\u0418\u0434\u0435\u0430\u043B\u044C\u043D\u043E\u0435 \u0440\u0435\u0448\u0435\u043D\u0438\u0435!'};

  var userLines=normUser.split('\n').filter(function(l){return l.trim()});
  var correctLines=normCorrect.split('\n').filter(function(l){return l.trim()});

  var allFound=true;
  var missingLines=[];
  for(var i=0;i<correctLines.length;i++){
    var cLine=correctLines[i].trim();
    var found=false;
    for(var j=0;j<userLines.length;j++){if(userLines[j].trim()===cLine){found=true;break;}}
    if(!found){allFound=false;missingLines.push(cLine);}
  }
  if(allFound)return{ok:true,msg:'\u041E\u0442\u043B\u0438\u0447\u043D\u043E! \u0412\u0441\u0435 \u0441\u0442\u0440\u043E\u043A\u0438 \u043D\u0430\u0439\u0434\u0435\u043D\u044B!'};

  if(keywords&&keywords.length>0){
    var foundKw=[];var missingKw=[];
    for(var k=0;k<keywords.length;k++){
      var kw=keywords[k].toLowerCase().trim();
      if(kw&&normUser.indexOf(kw)!==-1)foundKw.push(kw);else missingKw.push(kw);
    }
    if(missingKw.length===0)return{ok:true,msg:'\u0412\u0441\u0435 \u043A\u043B\u044E\u0447\u0435\u0432\u044B\u0435 \u0441\u043B\u043E\u0432\u0430 \u043D\u0430\u0439\u0434\u0435\u043D\u044B!'};
    if(foundKw.length>0&&missingKw.length<=keywords.length/2)
      return{ok:false,msg:'\u041F\u043E\u0447\u0442\u0438 \u043F\u0440\u0430\u0432\u0438\u043B\u044C\u043D\u043E! \u041D\u0430\u0439\u0434\u0435\u043D\u044B: '+foundKw.join(', ')+'. \u041E\u0442\u0441\u0443\u0442\u0441\u0442\u0432\u0443\u044E\u044<think>: '+missingKw.join(', ')};
    return{ok:false,msg:'\u041D\u0435 \u0445\u0432\u0430\u0442\u0430\u0435\u0442 \u043A\u043B\u044E\u0447\u0435\u0432\u044B\u0445 \u0441\u043B\u043E\u0432: '+missingKw.join(', ')};
  }

  var matchCount=0;
  for(var m=0;m<correctLines.length;m++){
    var cl=correctLines[m].trim();
    for(var n=0;n<userLines.length;n++){if(userLines[n].indexOf(cl)!==-1||cl.indexOf(userLines[n].trim())!==-1){matchCount++;break;}}
  }
  var ratio=correctLines.length>0?matchCount/correctLines.length:0;
  if(ratio>=0.8)return{ok:true,msg:'\u0425\u043E\u0440\u043E\u0448\u043E! \u0411\u043E\u043B\u044C\u0448\u0438\u043D\u0441\u0442\u0432\u043E \u0441\u0442\u0440\u043E\u043A \u0441\u043E\u0432\u043F\u0430\u0434\u0430\u0435\u0442.'};
  return{ok:false,msg:'\u0421\u043E\u0432\u043F\u0430\u0434\u0435\u043D\u0438\u0435: '+Math.round(ratio*100)+'%. \u041F\u0440\u043E\u0432\u0435\u0440\u044C\u0442\u0435 \u0441\u0442\u0440\u0443\u043A\u0442\u0443\u0440\u0443 \u043A\u043E\u0434\u0430 \u043F\u043E \u043F\u0440\u0438\u043C\u0435\u0440\u0443.'};
}

function completeLvl(){
  var course=getCourse(S.curCourse);
  if(!course)return;
  if(!S.progress[course.id])S.progress[course.id]=0;
  S.progress[course.id]++;
  if(S.progress[course.id]>=course.levels.length){
    var idx=-1;for(var i=0;i<S.courses.length;i++)if(S.courses[i]===course){idx=i;break}
    if(idx+1<S.courses.length)S.courses[idx+1].locked=false;
    save();launchFW();
    setTimeout(function(){S.page='complete';render()},1000);
  }else{
    save();
    if(S.curLevel+1<course.levels.length){S.curLevel++;S.page='level';S.practiceRes=null;}
    else{S.page='course';}
    render();
  }
}

// ===== PRACTICE PROGRESS =====
function getPracticeProgress(courseId){
  var course=getCourse(courseId);
  if(!course||!course.practices)return{total:0,done:0};
  var total=course.practices.length;
  var done=0;
  for(var i=0;i<course.practices.length;i++){
    if(isPracticeDone(courseId,course.practices[i].id))done++;
  }
  return{total:total,done:done};
}

function isPracticeDone(courseId,practiceId){
  var key=courseId+'_'+practiceId;
  return!!S.practiceProgress[key];
}

function completeCoursePrac(){
  var course=getCourse(S.curCourse);
  if(!course)return;
  var key=S.curCourse+'_'+S.curPractice;
  S.practiceProgress[key]=true;
  save();
  S.page='course';
  S.curPractice=null;
  S.practiceRes=null;
  render();
}

// ===== COMPLETE =====
function rComplete(){
  var course=getCourse(S.curCourse);
  if(!course)return'';
  var next=null;var found=false;
  for(var j=0;j<S.courses.length;j++){if(found&&S.courses[j].locked){next=S.courses[j];break}if(S.courses[j].id===course.id)found=true}

  var nh='',nb='';
  if(next){nh='<div class="glass rounded-xl p-3 mb-5 border-amber-500/30"><p class="text-xs text-amber-400 mb-1">\uD83D\uDD13 \u041D\u043E\u0432\u044B\u0439 \u043A\u0443\u0440\u0441!</p><p class="text-white font-semibold text-sm">'+next.icon+' '+esc(next.title)+'</p></div>';
    nb='<button onclick="S.curCourse='+next.id+';S.page=\'course\';render()" class="px-5 py-2 rounded-lg bg-amber-500/20 text-amber-400 text-sm">\u041D\u0430\u0447\u0430\u0442\u044C '+esc(next.title)+'</button>';}

  return '<div class="min-h-screen hero-gradient flex items-center justify-center">'+
    '<div class="max-w-md mx-auto px-4 text-center">'+
    '<div class="glass-strong rounded-3xl p-8 slide-up" style="animation:celebrate .6s ease">'+
    '<div class="text-5xl mb-4">\uD83C\uDFC6</div>'+
    '<h2 class="text-2xl font-bold text-white mb-1 shimmer-text">\u041A\u0443\u0440\u0441 \u043F\u0440\u043E\u0439\u0434\u0435\u043D!</h2>'+
    '<p class="text-gray-400 text-sm mb-6">'+esc(course.title)+'</p>'+
    '<div class="grid grid-cols-3 gap-3 mb-6">'+
    '<div class="glass rounded-lg p-2"><div class="text-xl font-bold text-white">'+course.levels.length+'</div><div class="text-[10px] text-gray-500">\u0423\u0440\u043E\u0432\u043D\u0435\u0439</div></div>'+
    '<div class="glass rounded-lg p-2"><div class="text-xl font-bold text-green-400">100%</div><div class="text-[10px] text-gray-500">\u041F\u0440\u043E\u0433\u0440\u0435\u0441\u0441</div></div>'+
    '<div class="glass rounded-lg p-2"><div class="text-xl font-bold text-amber-400">\u2B50</div><div class="text-[10px] text-gray-500">\u0420\u0435\u0439\u0442\u0438\u043D\u0433</div></div></div>'+
    nh+
    '<div class="flex gap-2 justify-center">'+
    '<button onclick="goHome()" class="btn-primary px-5 py-2 rounded-lg text-white text-sm font-semibold">\uD83D\uDCDA \u041A\u0443\u0440\u0441\u044B</button>'+nb+'</div></div></div></div>';
}

// ===== PARTICLES & FIREWORKS =====
function initParticles(){
  var canvases=document.querySelectorAll('.particle-canvas');
  for(var c=0;c<canvases.length;c++){
    (function(cv){
      if(cv._p)return;cv._p=true;
      var st=cv.getAttribute('data-status');
      var cl=cv.getAttribute('data-color');
      var ctx=cv.getContext('2d');
      function rsz(){var r=cv.parentElement.getBoundingClientRect();cv.width=r.width;cv.height=r.height}
      rsz();
      var pts=[];var n=st==='active'?12:(st==='completed'?6:3);
      for(var i=0;i<n;i++)pts.push({x:Math.random()*150,y:Math.random()*40,vx:(Math.random()-.5)*.4,vy:(Math.random()-.5)*.4,sz:Math.random()*1.5+.5,al:Math.random()*.4+.1});
      (function anim(){
        ctx.clearRect(0,0,cv.width,cv.height);rsz();
        for(var i=0;i<pts.length;i++){
          var p=pts[i];p.x+=p.vx;p.y+=p.vy;
          if(p.x<0||p.x>cv.width)p.vx*=-1;
          if(p.y<0||p.y>cv.height)p.vy*=-1;
          ctx.beginPath();ctx.arc(p.x,p.y,p.sz,0,Math.PI*2);
          ctx.fillStyle=cl+Math.round(p.al*255).toString(16).padStart(2,'0');ctx.fill();
        }
        requestAnimationFrame(anim)})();
    })(canvases[c]);
  }
}

function launchFW(){
  var cv=document.getElementById('fw-canvas');if(!cv)return;
  var ctx=cv.getContext('2d');cv.width=window.innerWidth;cv.height=window.innerHeight;
  var fws=[],sps=[];
  var cls=['#6c5ce7','#a855f7','#ec4899','#f59e0b','#10b981','#3b82f6','#ef4444'];
  function mkF(){var x=Math.random()*cv.width;var ty=Math.random()*cv.height*.4+50;fws.push({x:x,y:cv.height,ty:ty,vy:-7-Math.random()*4,cl:cls[Math.floor(Math.random()*cls.length)],ex:false})}
  function exp(x,y,cl){for(var i=0;i<50;i++){var a=(Math.PI*2/50)*i+Math.random()*.2;var sp=2+Math.random()*3;sps.push({x:x,y:y,vx:Math.cos(a)*sp,vy:Math.sin(a)*sp,cl:cl,life:1,dc:.015+Math.random()*.015,sz:2+Math.random()*2})}}
  var fr=0;
  (function anim(){
    if(fr>200){ctx.clearRect(0,0,cv.width,cv.height);return}fr++;
    ctx.fillStyle='rgba(10,10,15,.2)';ctx.fillRect(0,0,cv.width,cv.height);
    if(fr%25===0)mkF();
    for(var i=0;i<fws.length;i++){var f=fws[i];if(!f.ex){f.y+=f.vy;f.vy+=.1;ctx.beginPath();ctx.arc(f.x,f.y,3,0,Math.PI*2);ctx.fillStyle=f.cl;ctx.fill();if(f.y<=f.ty||f.vy>0){f.ex=true;exp(f.x,f.y,f.cl)}}}
    for(var j=0;j<sps.length;j++){var s=sps[j];s.x+=s.vx;s.y+=s.vy;s.vy+=.05;s.life-=s.dc;if(s.life>0){ctx.beginPath();ctx.arc(s.x,s.y,s.sz*s.life,0,Math.PI*2);ctx.fillStyle=s.cl+Math.round(s.life*255).toString(16).padStart(2,'0');ctx.fill()}}
    requestAnimationFrame(anim)})()}

load();initDefaults();render();
window.addEventListener('resize',function(){var f=document.getElementById('fw-canvas');if(f){f.width=window.innerWidth;f.height=window.innerHeight}});
</script>
</body>
</html>

