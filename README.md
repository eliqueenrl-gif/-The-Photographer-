<!doctype html>
<html lang="ko">
<head>
<meta charset="utf-8">
<title>The Photographer</title>
<style>
  *{box-sizing:border-box;margin:0;padding:0;font-family:'Noto Serif KR','Nanum Myeongjo',serif;-webkit-tap-highlight-color:transparent}
  html,body{width:100%;height:100%;background:#000;overflow:hidden}
  #wrap{position:absolute;left:50%;top:50%;width:1920px;height:1080px;transform-origin:center center;background:#111}
  #stage{position:relative;width:100%;height:100%;overflow:hidden;background:#1a1a1a}
  .layer{position:absolute;inset:0;width:100%;height:100%}
  #bg{background-size:cover;background-position:center;background-repeat:no-repeat;background-color:#222}

  /* === standing illust : 크기 키움 (말풍선 뒤로 겹쳐도 OK) === */
  .stand{position:absolute;bottom:0;width:620px;height:1000px;display:none;z-index:5;pointer-events:none}
  .stand img{width:100%;height:100%;object-fit:contain;object-position:bottom;display:block}
  #standL{left:80px}
  #standR{right:80px}

  /* === subtitle box : 기존(1번) 모양 유지, 색깔만 와인/크림으로 변경 === */
  #sub{position:absolute;left:160px;right:160px;bottom:60px;height:280px;
       background:#2b0a17;border:3px solid #f5e9d6;border-radius:14px;
       padding:48px 80px 60px 80px;color:#f5e9d6;font-size:34px;line-height:1.5;z-index:25;display:none;
       box-shadow:0 12px 40px rgba(0,0,0,0.6)}
  #speaker{position:absolute;top:-22px;left:50%;transform:translateX(-50%);
           background:#2b0a17;border:3px solid #f5e9d6;border-radius:10px;
           padding:6px 28px;font-size:28px;color:#f5e9d6;display:none;letter-spacing:2px}
  #text{white-space:pre-wrap;margin-top:8px}
  #next{position:absolute;right:24px;bottom:18px;width:64px;height:54px;
        background:#f5e9d6;color:#2b0a17;border:none;font-size:32px;border-radius:8px;cursor:pointer;display:none}

  /* === choices : 기존(1번) 모양 유지, 색깔만 변경 === */
  #choices{position:absolute;left:50%;transform:translateX(-50%);bottom:120px;width:900px;z-index:26;display:none;flex-direction:column;gap:24px;align-items:center}
  .choice{width:100%;background:#2b0a17;border:3px solid #f5e9d6;color:#f5e9d6;font-size:30px;
          padding:24px 30px;border-radius:12px;cursor:pointer;text-align:center;font-family:inherit;letter-spacing:1px}
  .choice:hover{background:#4a1626}

  /* chapter title */
  #chapter{position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);
           color:#f5e9d6;font-size:72px;background:#2b0a17;border:3px solid #f5e9d6;border-radius:14px;
           padding:40px 80px;opacity:0;transition:opacity 1s ease;z-index:30;display:none;letter-spacing:4px}

  /* fade & flash */
  #fade{position:absolute;inset:0;background:#000;opacity:0;pointer-events:none;transition:opacity 0.8s ease;z-index:40}
  #flash{position:absolute;inset:0;background:#fff;opacity:0;pointer-events:none;transition:opacity 0.3s ease;z-index:45}

  /* opening narration */
  #narr{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;
        color:#f5e9d6;font-size:56px;text-align:center;opacity:0;transition:opacity 1.2s ease;z-index:50;
        pointer-events:none;padding:0 120px;letter-spacing:3px}

  /* skip */
  #skip{position:absolute;top:30px;right:30px;background:#f5e9d6;color:#2b0a17;border:none;
        font-size:24px;padding:14px 26px;border-radius:10px;cursor:pointer;z-index:80;display:none;font-family:inherit}

  /* desk objects : 레퍼런스대로 책은 엄청 크게, 커피 버튼은 좌상단으로 */
  .obj{position:absolute;cursor:default;transition:transform 0.2s}
  .obj.active{cursor:pointer}
  .obj.active:hover{transform:scale(1.04)}
  /* 닫힌 책: 1073x880, 화면 하단에 찰싹 (left=(1920-1073)/2≈424, top=1080-880=200) */
  #book{left:424px;top:200px;width:1073px;height:880px;z-index:5}
  /* 펼친 책: 1612x978, 중앙. 펼쳤을 땐 커피 버튼보다 위로 */
  #book.open{left:154px;top:51px;width:1612px;height:978px;z-index:20}
  #book img{width:100%;height:100%;object-fit:contain}
  /* 커피 버튼: 788x668, 좌상단 끝 찰싹 */
  #coffeeBtn{left:0;top:0;width:788px;height:668px;z-index:10}
  #coffeeBtn img{width:100%;height:100%;object-fit:contain}

  /* author note modal */
  #author{position:absolute;left:200px;top:120px;right:200px;bottom:120px;
          background:#f0e3cc;border:6px solid #2b0a17;border-radius:14px;z-index:90;display:none;padding:60px;
          color:#2b0a17;font-size:28px;line-height:1.7}
  #authorClose{position:absolute;right:24px;bottom:24px;background:#2b0a17;color:#f5e9d6;border:none;
               padding:14px 28px;font-size:24px;border-radius:8px;cursor:pointer;font-family:inherit}

  /* name input */
  #nameBox{position:absolute;inset:0;display:none;align-items:center;justify-content:center;flex-direction:column;color:#f5e9d6;z-index:60;gap:30px;background:rgba(0,0,0,0.85)}
  #nameBox h1{font-size:54px;letter-spacing:4px}
  #nameBox input{font-size:32px;padding:14px 20px;width:520px;text-align:center;border-radius:8px;border:2px solid #f5e9d6;background:#2b0a17;color:#f5e9d6;font-family:inherit}
  #nameBox button{font-size:28px;padding:14px 40px;border-radius:8px;border:2px solid #f5e9d6;background:#2b0a17;color:#f5e9d6;cursor:pointer;font-family:inherit}

  /* photo display */
  #photo{position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);
         width:1820px;height:980px;object-fit:contain;border:6px solid #f5e9d6;display:none;z-index:55;background:#333;
         box-shadow:0 20px 60px rgba(0,0,0,0.8)}

  /* restart center */
  #restartCenter{position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);z-index:70;display:none;flex-direction:column;gap:24px}
  #restartCenter button{font-size:32px;padding:18px 50px;border-radius:10px;border:3px solid #f5e9d6;background:#2b0a17;color:#f5e9d6;cursor:pointer;font-family:inherit;letter-spacing:2px}

  /* === ending album : 검은 배경 + scenefailN 이미지 === */
  #album{position:absolute;inset:0;background:#000;z-index:65;display:none;opacity:0;transition:opacity 0.7s ease}
  #albumBook{position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);width:1200px;height:840px;object-fit:contain}

  /* === ending message text === */
  #endMsg{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;flex-direction:column;
          color:#f5e9d6;font-size:48px;text-align:center;opacity:0;z-index:66;
          pointer-events:none;padding:0 120px;letter-spacing:3px;line-height:1.6;white-space:pre-line;
          transition:opacity 2s ease}
</style>
</head>
<body>
<div id="wrap">
<div id="stage">
  <div class="layer" id="bg"></div>

  <div class="stand" id="standL"><img id="imgL" src="" alt=""></div>
  <div class="stand" id="standR"><img id="imgR" src="" alt=""></div>

  <div class="obj" id="book"><img id="bookImg" src="book_closed.png" alt=""></div>
  <div class="obj" id="coffeeBtn"><img src="coffee_button.png" alt=""></div>

  <img id="photo" src="" alt="">
  <div id="flash" class="layer"></div>

  <div id="sub">
    <div id="speaker"></div>
    <div id="text"></div>
    <button id="next">▶</button>
  </div>
  <div id="choices"></div>

  <div id="chapter"></div>
  <div id="narr"></div>

  <div id="nameBox">
    <h1>당신의 이름은?</h1>
    <input id="nameInput" maxlength="12" placeholder="이름 입력">
    <button id="nameDone">완성</button>
  </div>

  <div id="author">
    <h2 style="font-size:38px;margin-bottom:24px;letter-spacing:4px">작가의 말</h2>
    <p style="text-align:left;line-height:1.7;white-space:pre-line;font-size:16px">안녕하세요.
저희는 'The Photographer' 게임 제작을 맡은 영신여고 3학년에 재학중인 조예은, 유이영, 원엘리퀸 입니다.
해당 사이트는 그림을 제외한 코딩 부분에서 AI를 사용했습니다.

'The Photographer'는 '사라진 이야기를 복구하는 게임'이라는 컨셉의 선택형 스토리텔링 비주얼 노벨 게임으로, 어려운 고전 문학을 게임을 통해 쉽게 다가갈 수 있도록 만들었습니다.
플레이어는 고전 문학 작품이 사라져가는 세계 속 '소설 사진가'가 되어 &lt;도리언 그레이의 초상화&gt;를 기반으로 한 이야기를 복구하며 원작을 지키거나 왜곡된 결말에 도달하게 됩니다.

이제 당신은 마지막 독자가 될지도 모릅니다.
이야기의 흔적에 따라 잊혀진 기록들을 되돌려 원작을 복구하세요.

ps. 도리언 그레이의 초상 꼭 읽어주세요</p>
    <button id="authorClose">닫기</button>
  </div>

  <div id="restartCenter"></div>

  <div id="album">
    <img id="albumBook" src="" alt="">
  </div>
  <div id="endMsg"></div>

  <div id="fade" class="layer"></div>
  <button id="skip">스킵 ▶▶</button>
  <div id="startGate" style="position:absolute;inset:0;background:#000;display:flex;align-items:center;justify-content:center;z-index:9999;cursor:pointer;color:#f5e9d6;font-size:42px;letter-spacing:6px;font-family:'Noto Serif KR','Nanum Myeongjo',serif">클릭하여 시작</div>
</div>
</div>

<!-- audio -->
<audio id="aSub" src="subtitle.mp3" preload="auto"></audio>
<audio id="aCam" src="camera.mp3" preload="auto"></audio>
<audio id="aClock" src="clock.mp3" preload="auto"></audio>
<audio id="aTel" src="telephone.mp3" preload="auto" loop></audio>
<audio id="aBgm" src="" preload="auto" loop></audio>

<script>
/* ============ scale ============ */
function resize(){
  const w=innerWidth,h=innerHeight,s=Math.min(w/1920,h/1080);
  document.getElementById('wrap').style.transform=`translate(-50%,-50%) scale(${s})`;
}
addEventListener('resize',resize);resize();

/* ============ helpers ============ */
const $=id=>document.getElementById(id);
const sleep=ms=>new Promise(r=>setTimeout(r,ms));
const state={name:'플레이어',cancelTokens:[],freePlayReady:false,collected:[]};

/* speaker name -> character image */
const CHAR_IMG={
  '바질':'basil_png.png',
  '시빌':'sibyl_png.png',
  '도리언':'dorian_png.png',
  '헨리':'henry_png.png'
};

function newToken(){const t={cancelled:false};state.cancelTokens.push(t);return t;}
function cancelAll(){state.cancelTokens.forEach(t=>t.cancelled=true);state.cancelTokens=[];}
function setBG(src){$('bg').style.backgroundImage=src?`url(${src})`:'none';}
function playSE(id){try{const a=$(id);a.currentTime=0;a.play().catch(()=>{});}catch(e){}}
function stopSE(id){try{const a=$(id);a.pause();a.currentTime=0;}catch(e){}}
function setBGM(src){const a=$('aBgm');if(a.src.endsWith(src))return;a.src=src;a.volume=0.5;a.play().catch(()=>{});}
function stopBGM(){$('aBgm').pause();}

async function fadeBlackIn(){playSE('aClock');$('fade').style.opacity=1;await sleep(900);}
async function fadeBlackOut(){$('fade').style.opacity=0;await sleep(900);stopSE('aClock');}
async function cameraFlash(){playSE('aCam');$('flash').style.opacity=1;await sleep(300);$('flash').style.opacity=0;await sleep(300);}

function hideAllChars(){
  ['standL','standR'].forEach(id=>{$(id).style.display='none';});
  ['imgL','imgR'].forEach(id=>{$(id).src='';});
}
/* 기존(1번) 패턴: 좌/우 슬롯에 캐릭터 등장 */
function showChar(name, side){
  side = (side==='L')?'L':'R';
  const file = CHAR_IMG[name] || name;
  $('img'+side).src = file;
  $('stand'+side).style.display = 'block';
}

function hideSub(){$('sub').style.display='none';$('text').textContent='';$('speaker').style.display='none';$('next').style.display='none';}
function hideChoices(){$('choices').style.display='none';$('choices').innerHTML='';}

/* typewriter */
async function say(speaker, text, token){
  $('sub').style.display='block';
  if(speaker){$('speaker').textContent=speaker;$('speaker').style.display='block';}
  else{$('speaker').style.display='none';}
  $('text').textContent='';
  $('next').style.display='none';
  const chars=[...text];
  for(let i=0;i<chars.length;i++){
    if(token.cancelled)return;
    $('text').textContent+=chars[i];
    if(chars[i].trim())playSE('aSub');
    await sleep(60);
  }
  if(token.cancelled)return;
  $('next').style.display='block';
  await new Promise(res=>{
    const h=()=>{$('next').removeEventListener('click',h);res();};
    $('next').addEventListener('click',h);
    const iv=setInterval(()=>{if(token.cancelled){clearInterval(iv);$('next').removeEventListener('click',h);res();}},100);
  });
  $('next').style.display='none';
}

async function sayAuto(speaker,text,token){
  $('sub').style.display='block';
  if(speaker){$('speaker').textContent=speaker;$('speaker').style.display='block';}
  else $('speaker').style.display='none';
  $('text').textContent='';$('next').style.display='none';
  const chars=[...text];
  for(let i=0;i<chars.length;i++){
    if(token.cancelled)return;
    $('text').textContent+=chars[i];
    if(chars[i].trim())playSE('aSub');
    await sleep(60);
  }
  if(token.cancelled)return;
  await sleep(1000);
}

function choose(options,token){
  return new Promise(res=>{
    $('choices').style.display='flex';
    $('choices').innerHTML='';
    options.forEach((o,i)=>{
      const b=document.createElement('button');
      b.className='choice';b.textContent=o;
      b.onclick=()=>{if(token.cancelled)return;hideChoices();res(i);};
      $('choices').appendChild(b);
    });
  });
}

async function chapterTitle(title,token){
  $('chapter').textContent='" '+title+' "';
  $('chapter').style.display='block';
  await sleep(50);
  $('chapter').style.opacity=1;
  await sleep(1400);
  if(token.cancelled){$('chapter').style.display='none';$('chapter').style.opacity=0;return;}
  $('chapter').style.opacity=0;
  await sleep(1200);
  $('chapter').style.display='none';
}

async function showPhoto(src,token){
  /* 자막 끝나고 1초 텀 → 흰 페이드 인 → (인 직후) 사진 표시 2초 → (아웃 직전) 사진 숨김 → 흰 페이드 아웃 */
  await sleep(1000);
  if(token.cancelled) return;
  playSE('aCam');
  $('flash').style.opacity=1;
  await sleep(300);
  if(token.cancelled){$('flash').style.opacity=0;return;}
  $('photo').src=src;$('photo').style.display='block';
  await sleep(2000);
  $('photo').style.display='none';
  if(token.cancelled){$('flash').style.opacity=0;return;}
  $('flash').style.opacity=0;
  await sleep(300);
}

function showRestartButton(label,fn){
  const r=$('restartCenter');r.innerHTML='';r.style.display='flex';
  const b=document.createElement('button');b.textContent=label;
  b.onclick=()=>{r.style.display='none';fn();};
  r.appendChild(b);
}

/* ============ ENDING SEQUENCE ============
   endSequence(stage, success)
   stage 1~4 & success=false → scenefail{stage}.png
   stage 5      & success=true  → scenefail5.png
*/
async function endSequence(stage, success){
  cancelAll();
  const t=newToken();
  hideSub();hideChoices();hideAllChars();
  $('book').style.display='none';$('coffeeBtn').style.display='none';
  $('photo').style.display='none';
  $('chapter').style.display='none';$('chapter').style.opacity=0;
  stopBGM();

  // 1) 검은 화면
  $('fade').style.opacity=1;
  await sleep(800);

  // 2) 안내 문구 2초 페이드 인/아웃
  const msg = success
    ? '[ 사라진 장면을 되찾았다. ]\n\n→ 당신은 원래의 이야기를 지켜냈다.'
    : '[ 이 장면은 원래의 이야기와 달랐다. ]\n\n→ 소설 복구에 실패했다.';
  $('endMsg').textContent = msg;
  $('endMsg').style.opacity=1;
  await sleep(2000+800);
  $('endMsg').style.opacity=0;
  await sleep(2000);
  $('endMsg').textContent='';

  // 3) scenefailN.png 페이드 인 (책+모은 사진이 합쳐진 단일 이미지)
  $('albumBook').src = 'scenefail'+stage+'.png';
  $('album').style.display='block';
  await sleep(30);
  $('album').style.opacity=1;
  await sleep(700);

  // 4) 1.2초 유지
  await sleep(1200);

  // 5) 앨범 + 검은 화면 페이드 아웃
  $('album').style.opacity=0;
  await sleep(700);
  $('album').style.display='none';

  // 6) 다시 시작하기 버튼
  showRestartButton('다시 시작하기',()=>freePlay());
}


/* ============ OPENING ============ */
async function opening(){
  cancelAll();
  const t=newToken();
  hideSub();hideChoices();hideAllChars();
  $('skip').style.display='block';
  $('book').classList.remove('active');$('coffeeBtn').classList.remove('active');
  state.freePlayReady=false;
  setBG('intro_background.png');
  $('book').style.display='none';$('coffeeBtn').style.display='none';
  const lines=['먼 미래, 사람들은 점점 고전을 잊어갔다.','읽히지 않는 이야기들은 점점 사라져갔다.','그리고 나는 그 이야기들을 되돌리는 사람이다.'];
  $('fade').style.opacity=1;
  for(const l of lines){
    if(t.cancelled)return;
    $('narr').textContent=l;
    $('narr').style.opacity=1;
    await sleep(2800);
    if(t.cancelled)return;
    $('narr').style.opacity=0;
    await sleep(1400);
  }
  $('narr').textContent='';
  if(t.cancelled)return;
  setBG('desk_background.png');
  $('book').style.display='block';$('coffeeBtn').style.display='block';
  $('bookImg').src='book_closed.png';$('book').classList.remove('open');
  await sleep(200);
  $('fade').style.opacity=0;
  await sleep(900);
  if(t.cancelled)return;
  await sleep(2200);
  if(t.cancelled)return;
  playSE('aTel');
  await say(null,'전화가 왔다.',t);
  if(t.cancelled)return;
  await sayAuto(null,'전화를 받을까?',t);
  if(t.cancelled)return;
  await choose(['전화 받기'],t);
  stopSE('aTel');
  await say(null,'(달칵)',t);
  await say('의뢰자','소설 사진가 맞으시죠?',t);
  await choose(['예'],t);
  await say('의뢰자','이번 의뢰는 <도리언 그레이의 초상> 복구입니다.',t);
  await choose(['어떤 의뢰인지 묻는다.'],t);
  await say('의뢰자','늘 하시던 대로, 소설의 중요한 장면들을 복구하는 겁니다.',t);
  await choose(['…..'],t);
  await say('의뢰자','책 속에 들어가서 카메라로 중요한 장면들을 찍으면 소설이 복구됩니다.',t);
  await choose(['그렇군요.'],t);
  await say('의뢰자','그럼, 이번 의뢰도 잘 부탁드립니다.',t);
  await choose(['알겠습니다.'],t);
  await say(null,'(전화가 끊겼다.)',t);
  if(t.cancelled)return;
  await freePlay();
}

/* ============ FREE PLAY ============ */
async function freePlay(){
  cancelAll();
  const t=newToken();
  state.collected=[];
  hideSub();hideChoices();hideAllChars();
  $('chapter').style.display='none';$('chapter').style.opacity=0;
  $('photo').style.display='none';
  stopSE('aTel');
  await fadeBlackIn();
  setBG('desk_background.png');
  $('book').style.display='block';$('coffeeBtn').style.display='block';
  $('bookImg').src='book_closed.png';$('book').classList.remove('open');
  setBGM('scene0.mp3');
  await fadeBlackOut();
  state.freePlayReady=true;
  $('book').classList.add('active');$('coffeeBtn').classList.add('active');
  $('skip').style.display='none';
}

$('coffeeBtn').addEventListener('click',()=>{if(state.freePlayReady)$('author').style.display='block';});
$('authorClose').onclick=()=>{$('author').style.display='none';};

$('book').addEventListener('click',async ()=>{
  if(!state.freePlayReady)return;
  state.freePlayReady=false;
  $('book').classList.remove('active');$('coffeeBtn').classList.remove('active');
  $('bookImg').src='book_open.png';$('book').classList.add('open');
  const t=newToken();
  await sleep(1000);
  await say(null,'책 내용이 비어 있다.',t);
  await choose(['책 내용 복구하기'],t);
  $('book').style.display='none';$('coffeeBtn').style.display='none';
  await fadeBlackIn();
  hideSub();hideChoices();
  await part1();
});

/* ============ NAME ============ */
async function askName(){
  return new Promise(res=>{
    $('nameBox').style.display='flex';
    $('nameInput').value='';$('nameInput').focus();
    $('nameDone').onclick=()=>{
      const v=$('nameInput').value.trim()||'플레이어';
      state.name=v;$('nameBox').style.display='none';res();
    };
  });
}

/* ============ PART 1 ============ */
async function part1(){
  cancelAll();const t=newToken();
  $('fade').style.opacity=1;
  await askName();
  setBG('scene1_bg.png');setBGM('scene1.mp3');
  await fadeBlackOut();
  await chapterTitle('초상화 공개',t);
  await sleep(400);
  await say(null,'바질이 이젤을 돌려 친구들에게 초상화를 보여준다.',t);
  await say(null,'모두들 호평일색인 가운데 헨리 경이 당신에게 의견을 묻는다.',t);
  showChar('헨리','L');
  await sayAuto('헨리',`${state.name}, 자네는 어떻게 생각하나?`,t);
  const c=await choose(['같이 환호한다.','혹평한다.'],t);
  if(c===0){
    hideAllChars();
    await say(state.name,'정말 멋진데요! 도리언의 미모를 그대로 담은 것 같아요.',t);
    await say(null,'도리언은 초상화를 보며 자신의 미모에 심취한다.',t);
    showChar('도리언','R');
    await say('도리언','정말 슬픈일이에요!',t);
    await say('도리언','저는 언젠가 늙고 추해지겠죠, 하지만 이 초상화는 6월의 어느 날에서 한순간도 변하지 않을거예요.',t);
    await say('도리언','차라리 반대라면, 그럴 수만 있다면 무슨 짓이든 하겠어요!',t);
    hideSub();hideAllChars();
    state.collected.push('A');
    await showPhoto('sceneA_bg.png',t);
    await fadeBlackIn();
    await sleep(1500);
    await part2();
  } else {
    hideAllChars();
    await say(state.name,'정말 형편없네요! 화랑에서도 이런 그림은 받아주지 않을 거예요.',t);
    await say(null,'당신의 말에 모두들 당황한 기색을 보인다.',t);
    showChar('바질','R');
    await say('바질','아 그런가요...',t);
    hideAllChars();
    await say(null,'바질은 꽤나 상처받은 모양이다.',t);
    await say(null,'그 후로 바질의 작업실은 물론 사교계에서의 파티에도 초대받지 못한 채 시간이 흐른다.',t);
    hideSub();
    await endSequence(1,false);
  }
}

/* ============ PART 2 ============ */
async function part2(){
  cancelAll();const t=newToken();
  hideSub();hideChoices();hideAllChars();
  setBG('scene2.1_bg.png');setBGM('scene2.mp3');
  await fadeBlackOut();
  await chapterTitle('약혼자 시빌의 자살',t);
  await sleep(400);
  await say(null,'당신은 시빌의 대기실 앞에 다다른다.',t);
  await say(null,'대기실 안에서는 작은 소란이 들린다.',t);
  await say('시빌','...연기하는거죠?',t);
  await say('도리언','연기? 그딴 건 당신이나 해!\n아주 잘 하잖아?',t);
  const c=await choose(['대기실을 그냥 지나간다.','대기실에 들어간다.'],t);
  if(c===0){
    await say(null,'당신은 도리언과 시빌의 말다툼이 심화되는 상황에서 기록만 한 채 그대로 집으로 돌아갔습니다.',t);
    await say(null,'그 다음날 신문에서는 시빌로 추정되는 소녀의 극단적 선택에 대한 이야기가 보도되었습니다.',t);
    hideSub();
    state.collected.push('B');
    await showPhoto('sceneB_bg.png',t);
    await fadeBlackIn();
    await sleep(1500);
    await part3();
  } else {
    setBG('scene2_bg.png');
    await say(state.name,'그만둬요!',t);
    showChar('시빌','L');
    await say('시빌','왜요?\n내가 사랑하는 사람은 날 떠났어요!\n제 삶은 도리언이 없으면 더 이상 의미가 없다고요!',t);
    hideAllChars();
    await say(state.name,'그래도 그런 선택을 하면 어떡해요?\n한번이라도 붙잡아 볼 순 없는 건가요?',t);
    showChar('시빌','L');
    await say('시빌','...역시 그래봐야 겠어요.',t);
    hideAllChars();
    await say(null,'시빌은 대기실 밖으로 뛰쳐나갑니다.',t);
    hideSub();
    await fadeBlackIn();
    await sleep(1000);
    showRestartButton('다시 시작하기',()=>freePlay());
  }
}

/* ============ PART 3 ============ */
async function part3(){
  cancelAll();const t=newToken();
  hideSub();hideChoices();hideAllChars();
  setBG('scene3_bg.png');setBGM('scene3.mp3');
  await fadeBlackOut();
  await chapterTitle('물욕',t);
  await sleep(400);
  await say(null,'당신은 도리언과 지중해 연안에 있는 지역으로 여행을 가 카페에서 대화를 나눕니다.',t);
  await say(null,'도리언은 여행 내내 사치스러운 나날을 보내고 낭비를 일삼습니다.',t);
  showChar('도리언','L');
  await say('도리언','당신이 생각하기에는 어떤가요?',t);
  await sayAuto('도리언','아까 광장에 있던 크라바트가 눈에 아른거리네요.',t);
  const c=await choose(['도리언의 사치스러움에 대해 이야기한다.','도리언의 말에 동조한다.'],t);
  if(c===0){
    hideAllChars();
    await say(null,'당신은 그런 그의 소비습관이 못마땅해 한마디 하기로 합니다.',t);
    await say(state.name,'도리언, 아무리 생각해도 자네 씀씀이가 너무 큰 것 같네.',t);
    await say(state.name,'맨날 펑펑 쓰기만 하다간 늙어서는 지팡이 살 돈도 없겠어.',t);
    await say(null,'당신의 설교에 도리언은 무언가 깨달음을 얻은 듯 보입니다.',t);
    showChar('도리언','L');
    await say('도리언','당신 말이 맞아요.',t);
    await say('도리언','그동안 너무 나태하게 살긴 했죠.',t);
    await say('도리언','저택에 돌아가면 집사에게 밀린 업무를 물어봐야겠어요.',t);
    hideSub();hideAllChars();
    await endSequence(3,false);
  } else {
    await say(state.name,'분명 잘 어울릴 거예요.',t);
    await say('도리언','하하, 안 어울릴 이유야 없죠! 당연히 어울리겠지.',t);
    await say('도리언','그냥 광장에 다시 가는 번거로움에 대해 묻는 겁니다.',t);
    hideAllChars();
    setBG('scene3.1_bg.png');
    await say(null,'도리언은 자신만만한 표정으로 수표를 쓴 뒤 점원에게 건네고 광장으로 갈 채비를 합니다.',t);
    hideSub();
    state.collected.push('C');
    await showPhoto('sceneC_bg.png',t);
    await fadeBlackIn();
    await sleep(1500);
    await part4();
  }
}

/* ============ PART 4 ============ */
async function part4(){
  cancelAll();const t=newToken();
  hideSub();hideChoices();hideAllChars();
  setBG('scene4_bg.png');setBGM('scene4.mp3');
  await fadeBlackOut();
  await chapterTitle('바질의 죽음',t);
  await sleep(400);
  await say(null,'당신은 도리언의 저택 문 앞에 서있는 바질을 마주칩니다.',t);
  await say(state.name,'도리언의 집에는 어쩐 일이에요?',t);
  showChar('바질','L');
  await say('바질','저 좀 오랫동안 프랑스로 떠나요.',t);
  await say('바질','당신한테도 인사하고 싶었는데 이렇게 기회가 되어서 좋네요.',t);
  await say('바질','도리언의 집에 온 건...',t);
  await say('바질','아무래도 마지막 인사를 전해야 할 것 같아서요.',t);
  await sayAuto('바질','그런데 왜인지 망설여지네요…',t);
  const c=await choose(['도리언에게 마지막 인사를 하라고 말한다.','그냥 평소처럼 대한 뒤 떠나라고 말한다.'],t);
  if(c===0){
    hideAllChars();
    await say(state.name,'당연히 인사를 해야죠. 둘 사이는 각별하잖아요.',t);
    await say(state.name,'말없이 가버리면 서운할거에요.',t);
    showChar('바질','L');
    await say('바질','하하 각별하다니...',t);
    await say('바질','그렇게 보인다면 인사정돈 해야겠어요.',t);
    hideSub();hideAllChars();
    state.collected.push('D');
    await showPhoto('sceneD_bg.png',t);
    await fadeBlackIn();
    await sleep(1500);
    await part5();
  } else {
    await say(state.name,'도리언 요새 성격 알잖아요.',t);
    await say(state.name,'갑자기 떠난다고 말하면 가만 안 있을걸요.',t);
    showChar('바질','L');
    await say('바질','아, 역시 그러려나…',t);
    hideAllChars();
    await say(null,'바질은 끄덕이며 기차역 쪽으로 걸어갑니다.',t);
    hideSub();
    await endSequence(4,false);
  }
}

/* ============ PART 5 ============ */
async function part5(){
  cancelAll();const t=newToken();
  hideSub();hideChoices();hideAllChars();
  setBG('scene5_bg.png');setBGM('scene5.mp3');
  await fadeBlackOut();
  await chapterTitle('초상화 죽이기',t);
  await sleep(400);
  await say(null,'당신은 어딘가 어두운 표정으로 다락방 문을 열고 들어가는 도리언 그레이를 발견합니다.',t);
  await say(null,'아마도 원작대로 초상화를 없애려는 모양입니다.',t);
  await say(null,'당신은 도리언이 눈치 채지 못하게 다락방 문 앞으로 가 소리를 엿듣습니다.',t);
  showChar('도리언','R');
  await sayAuto('도리언','초상화를 없애고 난 자유로운 삶을 살게 되는 거야…',t);
  const c=await choose(['문을 열어 초상화를 없애지 못하도록 설득한다.','그대로 있는다.'],t);
  hideAllChars();
  if(c===0){
    await say(null,'당신은 문고리를 돌려 문을 열려고 합니다.',t);
    await say(null,'하지만 굳게 잠겨 열리지 않습니다.',t);
  } else {
    await say(null,'당신은 계속해서 도리언이 무슨 행동을 하고 있을지 추측해봅니다.',t);
  }
  await say('도리언','아아아아악!!!!!!!!!!',t);
  await say(null,'도리언의 비명에 많은 사람들이 모이기 시작합니다.',t);
  await say(null,'몇몇 사용인은 다락방 문을 열기 위해 계속해서 밀어내지만 실패합니다.',t);
  await say(null,'…………',t);
  await say(null,'곧이어 창문을 열고 들어간 방 안에는 야회복 차림의 늙은 노인이 칼이 박힌 채 쓰러져있습니다.',t);
  await say(null,'모여 있는 사람들은 그 노인의 주름진 손에 끼워져 있는 반지로 그가 누군지 조용히 알아차리곤 놀라움에 탄식합니다.',t);
  hideSub();
  state.collected.push('E');
  await showPhoto('sceneE_bg.png',t);
  await fadeBlackIn();
  await sleep(1500);
  await endSequence(5,true);
}

/* ============ SKIP ============ */
$('skip').addEventListener('click',()=>{cancelAll();hideSub();hideChoices();hideAllChars();$('chapter').style.display='none';$('chapter').style.opacity=0;$('narr').style.opacity=0;$('narr').textContent='';freePlay();});

/* boot: require user gesture to unlock audio autoplay */
document.getElementById('startGate').addEventListener('click',function(){
  // unlock all audio elements with a silent play/pause in the gesture
  ['aSub','aCam','aClock','aTel','aBgm'].forEach(id=>{
    const a=document.getElementById(id);
    try{a.play().then(()=>{a.pause();a.currentTime=0;}).catch(()=>{});}catch(e){}
  });
  this.remove();
  opening();
},{once:true});
</script>
</body>
</html>
</script>
</body>
</html>
