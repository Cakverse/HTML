# HTML
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Vocabulary Team Quiz</title>
<style>
  body {
    margin: 0;
    font-family: Arial, Helvetica, sans-serif;
    background: linear-gradient(#6ec6ff, #cfefff);
    color: #00324d;
  }
  .container { max-width: 1500px; margin: auto; padding: 20px; }
  h1 { text-align: center; font-size: 64px; }
  .team-switch { text-align: center; margin: 20px 0; }
  .team-switch button { font-size: 28px; padding: 10px 18px; margin: 5px; border-radius: 12px; border: none; cursor: pointer; }
  .scores { display: grid; grid-template-columns: repeat(5, 1fr); gap: 12px; margin-bottom: 20px; }
  .score-card { background: #fff; border-radius: 20px; padding: 15px; text-align: center; font-size: 30px; box-shadow: 0 4px 10px rgba(0,0,0,0.25); }
  .active { border: 6px solid #ff9800; }
  .question-box { background: white; border-radius: 24px; padding: 30px; box-shadow: 0 6px 15px rgba(0,0,0,0.25); }
  .image { text-align: center; font-size: 120px; margin-bottom: 20px; }
  .question { font-size: 42px; margin-bottom: 30px; text-align: center; }
  .answers { display: grid; grid-template-columns: repeat(2, 1fr); gap: 20px; }
  .answers button { font-size: 34px; padding: 22px; border-radius: 20px; border: none; cursor: pointer; background: #e3f2fd; }
  .answers button.correct { background: #4caf50; color: white; }
  .answers button.wrong { background: #f44336; color: white; display: none; }
  .status { text-align: center; font-size: 38px; margin-top: 20px; }
  .next { text-align: center; margin-top: 30px; }
  .next button { font-size: 34px; padding: 15px 35px; border-radius: 20px; border: none; cursor: pointer; }
</style>
</head>
<body>
<div class="container">
<h1>Vocabulary Team Quiz</h1>
<div class="team-switch" id="teamSwitch"></div>
<div class="scores" id="scores"></div>
<div class="question-box">
  <div class="image" id="image"></div>
  <div class="question" id="question"></div>
  <div class="answers" id="answers"></div>
  <div class="status" id="status"></div>
</div>
<div class="next"><button onclick="nextQuestion()">Next Question</button></div>
</div>

<script>
const teams = Array.from({length:5},(_,i)=>({name:`Team ${i+1}`,score:0}));
let currentTeam = 0;
let attemptsLeft = 3;
let qIndex = 0;

const questions = [
 {img:"🤝",q:"Rules for polite behavior in society or a group",a:"etiquette"},
 {img:"🌍",q:"Knowing about and respecting the culture of others",a:"cultural literacy"},
 {img:"⛔",q:"Not allowed because of strong cultural or religious rules",a:"taboo"},
 {img:"💊",q:"A medicine that helps you breathe more easily",a:"a decongestant"},
 {img:"🤒",q:"A feeling of illness caused by a disease",a:"symptom"},
 {img:"🏥",q:"A medical examination to test your health",a:"a check up"},
 {img:"📽️",q:"Devices used to display slides or videos on a screen",a:"projectors"},
 {img:"📢",q:"A formal public declaration",a:"announcements"},
 {img:"🎤",q:"Elevated stands for a speaker or microphone",a:"podiums"},
 {img:"🎮",q:"A remote device used to change slides",a:"page-turner"},
 {img:"📘",q:"Life story written by the person themselves",a:"autobiographies"},
 {img:"📗",q:"Life story written by another person",a:"biographies"},
 {img:"🦟",q:"Fabric equipment protecting people from insects",a:"the mosquito net"},
 {img:"🤪",q:"Pretty silly",a:"wackey"},
 {img:"🔬",q:"A very small cell seen only with a microscope",a:"microorganism"},
 {img:"🌊",q:"Water covering land that is normally dry",a:"flood"},
 {img:"☀️",q:"A long time with little or no rain",a:"drought"},
 {img:"🍽️",q:"A serious shortage of food",a:"famine"},
 {img:"💼",q:"To earn money to support yourself",a:"make a living"},
 {img:"🔄",q:"To decide differently than before",a:"change one's mind"},
 {img:"👑",q:"The ability to guide and organize people",a:"leadership skills"},
 {img:"🏛️",q:"The group of people who lead a country",a:"government"},
 {img:"👑",q:"A type of government in the past led by a king or queen",a:"monachary"},
 {img:"🗳️",q:"A government where leaders can be changed by voting",a:"demonracy"},
 {img:"🌊",q:"An area of sea partly surrounded by land",a:"gulf"},
 {img:"⭐",q:"Not as good as you expect",a:"it is overated"},
 {img:"⚠️",q:"Able to threaten your life",a:"dangerous"}
];

function renderTeams(){
 const scores=document.getElementById('scores'); scores.innerHTML='';
 teams.forEach((t,i)=>{
  const d=document.createElement('div');
  d.className='score-card'+(i===currentTeam?' active':'');
  d.innerHTML=`<strong>${t.name}</strong><br>${t.score} pts`;
  scores.appendChild(d);
 });
 const sw=document.getElementById('teamSwitch'); sw.innerHTML='';
 teams.forEach((_,i)=>{
  const b=document.createElement('button');
  b.textContent=`Switch to Team ${i+1}`;
  b.onclick=()=>{currentTeam=i;renderTeams();};
  sw.appendChild(b);
 });
}

function loadQuestion(){
 attemptsLeft=3;
 document.getElementById('status').textContent='';
 const q=questions[qIndex];
 document.getElementById('image').textContent=q.img;
 document.getElementById('question').textContent=q.q;
 const answers=document.getElementById('answers'); answers.innerHTML='';
 const opts=shuffle([q.a,...shuffle(questions.map(x=>x.a).filter(x=>x!==q.a)).slice(0,3)]);
 opts.forEach(o=>{
  const b=document.createElement('button'); b.textContent=o;
  b.onclick=()=>checkAnswer(b,o===q.a);
  answers.appendChild(b);
 });
}

function checkAnswer(btn,correct){
 if(correct){
  btn.classList.add('correct');
  teams[currentTeam].score+=10;
  document.getElementById('status').textContent='✅ Correct!';
  renderTeams();
 } else {
  attemptsLeft--;
  btn.classList.add('wrong');
  document.getElementById('status').textContent=`❌ Wrong! Attempts left: ${attemptsLeft}`;
  if(attemptsLeft<=0){ currentTeam=(currentTeam+1)%teams.length; renderTeams(); }
 }
}

function nextQuestion(){ qIndex=(qIndex+1)%questions.length; loadQuestion(); }
function shuffle(a){ return a.sort(()=>Math.random()-0.5); }

renderTeams(); loadQuestion();
</script>
</body>
</html>
