<!DOCTYPE html>
<html>
<head>
<title>Sambit Matrix System</title>

<style>
body{
margin:0;
background:black;
color:lime;
font-family:monospace;
text-align:center;
overflow-x:hidden;
}

canvas{
position:fixed;
top:0;
left:0;
z-index:-1;
}

input,textarea{
background:black;
color:lime;
border:1px solid lime;
padding:8px;
margin:5px;
}

button{
background:lime;
border:none;
padding:8px 15px;
cursor:pointer;
margin:5px;
}

.hidden{display:none;}
.warning{color:red;font-weight:bold;}

/* Game */
#board{
display:grid;
grid-template-columns:repeat(3,100px);
gap:5px;
justify-content:center;
margin:20px;
}

.cell{
width:100px;
height:100px;
background:#111;
color:lime;
font-size:40px;
display:flex;
align-items:center;
justify-content:center;
cursor:pointer;
border:1px solid lime;
}

/* Admin */
#admin{
background:#111;
padding:20px;
border:2px solid red;
margin-top:20px;
}

.bar{
width:200px;
height:20px;
background:#222;
margin:10px auto;
border:1px solid red;
}

.fill{height:100%;width:0%;background:red;}
.fill2{height:100%;width:0%;background:orange;}

.circle{
width:150px;
height:150px;
border-radius:50%;
background:conic-gradient(red 0deg,#222 0deg);
display:flex;
align-items:center;
justify-content:center;
margin:20px auto;
}

.circle-inner{
width:110px;
height:110px;
background:black;
border-radius:50%;
display:flex;
align-items:center;
justify-content:center;
color:red;
font-size:20px;
}
</style>
</head>
<body>

<canvas id="matrix"></canvas>

<h2>⚡ Sambit Matrix Terminal ⚡</h2>

<!-- LOGIN -->
<div id="login">
<input type="password" id="loginPass" placeholder="Enter Login Password">
<br>
<button onclick="login()">Login</button>
<p id="loginMsg"></p>
</div>

<!-- TERMINAL -->
<div id="user" class="hidden">
<div id="output"></div>
<br>
<input id="cmd" placeholder="Enter Command">
<button onclick="runCommand()">Run</button>
</div>

<!-- CODE INJECTOR -->
<div id="injector" class="hidden">
<h3>Code Injector</h3>
<textarea id="codeInput" style="width:80%;height:120px;"></textarea><br>
<button onclick="runCode()">Run</button>
<button onclick="closeInjector()">Close</button>
<iframe id="outputFrame" style="width:80%;height:150px;background:white;"></iframe>
</div>

<!-- GAME -->
<div id="game" class="hidden">
<h3>Tic Tac Toe</h3>
<button onclick="startTwoPlayer()">2 Player</button>
<button onclick="startComputer()">vs Computer</button>
<div id="board"></div>
<p id="gameStatus"></p>
<button onclick="resetGame()">Restart</button>
<button onclick="closeGame()">Close</button>
</div>

<!-- ADMIN -->
<div id="admin" class="hidden">
<h2 style="color:red;">⚠ ADMIN CONTROL ⚠</h2>

<p>🕒 <span id="liveTime"></span></p>
<p>📅 <span id="liveDate"></span></p>
<p>⏳ Login: <span id="loginTime">0</span>s</p>

<div class="circle">
<div class="circle-inner">
<span id="percent">0%</span>
</div>
</div>

<p>CPU Usage</p>
<div class="bar"><div id="cpuBar" class="fill"></div></div>

<p>RAM Usage</p>
<div class="bar"><div id="ramBar" class="fill2"></div></div>

<button onclick="logoutAdmin()">Logout</button>
</div>

<script>

/* LOGIN SYSTEM */
let USER_PASSWORD="sambit hacker";
let ADMIN_PASSWORD="sambit9090";
let waitingAdmin=false;

function login(){
let p = document.getElementById("loginPass").value;

if(p === USER_PASSWORD){
document.getElementById("login").classList.add("hidden");
document.getElementById("user").classList.remove("hidden");
document.getElementById("loginMsg").innerHTML="";
}else{
document.getElementById("loginMsg").innerHTML =
"<span class='warning'>Wrong Password</span>";
}
}

function runCommand(){
let c=cmd.value.trim();

if(waitingAdmin){
if(c===ADMIN_PASSWORD){
waitingAdmin=false;
user.classList.add("hidden");
admin.classList.remove("hidden");
startDashboard();
}else{
output.innerHTML="<span class='warning'>Wrong Admin Password</span>";
}
cmd.value=""; return;
}

if(c==="/help"){
output.innerHTML="/help /clear /game9811 /code /admin";
}
else if(c==="/clear"){ output.innerHTML=""; }
else if(c==="/game9811"){ game.classList.remove("hidden"); }
else if(c==="/code"){ injector.classList.remove("hidden"); }
else if(c==="/admin"){
output.innerHTML="Enter Admin Password:";
waitingAdmin=true;
}
else{ output.innerHTML="Unknown Command"; }

cmd.value="";
}

/* CODE INJECTOR */
function runCode(){
let code=codeInput.value;
let doc=outputFrame.contentDocument;
doc.open(); doc.write(code); doc.close();
}
function closeInjector(){ injector.classList.add("hidden"); }

/* TIC TAC TOE */
let boardState=[],currentPlayer="X",gameActive=false,vsComputer=false;

function startTwoPlayer(){vsComputer=false;initGame();}
function startComputer(){vsComputer=true;initGame();}

function initGame(){
boardState=["","","","","","","","",""];
currentPlayer="X"; gameActive=true; createBoard();
gameStatus.innerText="Player X Turn";
}

function createBoard(){
board.innerHTML="";
for(let i=0;i<9;i++){
let cell=document.createElement("div");
cell.className="cell";
cell.onclick=()=>move(i);
board.appendChild(cell);
}
}

function move(i){
if(boardState[i]||!gameActive)return;
boardState[i]=currentPlayer;
board.children[i].innerText=currentPlayer;
if(checkWin(currentPlayer)){
gameStatus.innerText=currentPlayer+" Wins!";
gameActive=false; return;
}
if(!boardState.includes("")){
gameStatus.innerText="Draw!"; gameActive=false; return;
}
currentPlayer=currentPlayer==="X"?"O":"X";
gameStatus.innerText="Player "+currentPlayer+" Turn";
if(vsComputer&&currentPlayer==="O") computerMove();
}

function computerMove(){
let empty=boardState.map((v,i)=>v===""?i:null).filter(v=>v!==null);
let r=empty[Math.floor(Math.random()*empty.length)];
move(r);
}

function checkWin(p){
let w=[[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];
return w.some(a=>a.every(i=>boardState[i]===p));
}
function resetGame(){initGame();}
function closeGame(){game.classList.add("hidden");}

/* ADMIN DASHBOARD */
let startTime,interval;

function startDashboard(){
startTime=Date.now();
updateClock();
setInterval(updateClock,1000);
interval=setInterval(updateStats,1000);
}

function updateClock(){
let n=new Date();
liveTime.innerText=n.toLocaleTimeString();
liveDate.innerText=n.toDateString();
}

function updateStats(){
let sec=Math.floor((Date.now()-startTime)/1000);
loginTime.innerText=sec;
let percent=Math.min(sec*5,100);
percentEl=document.getElementById("percent");
percentEl.innerText=percent+"%";
document.querySelector(".circle").style.background=
"conic-gradient(red "+(percent*3.6)+"deg,#222 0deg)";
cpuBar.style.width=Math.random()*100+"%";
ramBar.style.width=Math.random()*100+"%";
}

function logoutAdmin(){
    clearInterval(interval); // dashboard update stop
    document.getElementById("admin").classList.add("hidden"); // hide admin
    document.getElementById("user").classList.remove("hidden"); // show terminal again
    document.getElementById("output").innerHTML=""; // optional: clear terminal output
}

/* MATRIX RAIN */
let canvas=document.getElementById("matrix");
let ctx=canvas.getContext("2d");
canvas.height=window.innerHeight;
canvas.width=window.innerWidth;

let letters="01";
letters=letters.split("");
let fontSize=14;
let columns=canvas.width/fontSize;
let drops=[];
for(let x=0;x<columns;x++) drops[x]=1;

function draw(){
ctx.fillStyle="rgba(0,0,0,0.05)";
ctx.fillRect(0,0,canvas.width,canvas.height);
ctx.fillStyle="#0F0";
ctx.font=fontSize+"px monospace";
for(let i=0;i<drops.length;i++){
let text=letters[Math.floor(Math.random()*letters.length)];
ctx.fillText(text,i*fontSize,drops[i]*fontSize);
if(drops[i]*fontSize>canvas.height&&Math.random()>0.975)
drops[i]=0;
drops[i]++;
}
}
setInterval(draw,33);

</script>
</body>
</html>
