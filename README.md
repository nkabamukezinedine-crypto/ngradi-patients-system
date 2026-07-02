# <!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NGYEBI KASONGO GRADI - Système Patients</title>

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

<style>
*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:Inter,sans-serif;
}

body{
background:linear-gradient(135deg,#06121f,#0a2a44);
color:white;
}

header{
display:flex;
justify-content:space-between;
align-items:center;
padding:18px 8%;
background:rgba(0,90,180,.25);
backdrop-filter:blur(20px);
position:sticky;
top:0;
border-bottom:1px solid rgba(255,255,255,.08);
}

.logo{
font-weight:700;
color:#4db5ff;
letter-spacing:1px;
}

nav a{
margin-left:18px;
color:white;
text-decoration:none;
font-size:14px;
opacity:.85;
cursor:pointer;
}

nav a:hover{
color:#4db5ff;
opacity:1;
}

.profile{
display:flex;
align-items:center;
gap:10px;
}

.profile img{
width:42px;
height:42px;
border-radius:50%;
border:2px solid #4db5ff;
object-fit:cover;
}

section{
padding:60px 10%;
}

.card{
background:rgba(255,255,255,.06);
border:1px solid rgba(255,255,255,.1);
padding:20px;
border-radius:15px;
margin-top:15px;
backdrop-filter:blur(10px);
}

input,button{
width:100%;
padding:12px;
margin-top:10px;
border-radius:10px;
border:none;
outline:none;
}

button{
background:#4db5ff;
color:black;
font-weight:600;
cursor:pointer;
transition:.3s;
}

button:hover{
transform:scale(1.02);
background:#1e90ff;
}

.patient{
padding:10px;
margin-top:8px;
background:rgba(255,255,255,.05);
border-radius:10px;
cursor:pointer;
display:flex;
justify-content:space-between;
}

.patient:hover{
background:rgba(77,181,255,.2);
}

#details table{
width:100%;
margin-top:10px;
border-collapse:collapse;
}

#details td{
padding:10px;
border-bottom:1px solid rgba(255,255,255,.1);
}

.hidden{display:none;}
.center{text-align:center;}
</style>
</head>

<body>

<!-- SET PASSWORD -->
<div id="setup" class="center section">
<h2>Créer votre mot de passe</h2>
<input type="password" id="newPass" placeholder="Créer un mot de passe">
<button onclick="savePass()">Valider</button>
</div>

<!-- LOGIN -->
<div id="login" class="center section hidden">
<h2>Connexion</h2>
<input type="password" id="pass" placeholder="Mot de passe">
<button onclick="login()">Entrer</button>
</div>

<!-- APP -->
<div id="app" class="hidden">

<header>
<div class="logo">NGYEBI KASONGO GRADI</div>

<nav>
<a onclick="show('home')">Accueil</a>
<a onclick="show('about')">Savoir plus</a>
<a onclick="show('patients')">Patients</a>
</nav>

<div class="profile">
<input type="file" onchange="loadImg(event)" style="display:none" id="imgInput">
<img id="avatar" src="https://via.placeholder.com/100" onclick="document.getElementById('imgInput').click()">
</div>
</header>

<section id="home">
<h2>Dashboard</h2>
<div class="card">
Système de gestion des patients - version locale sécurisée.
</div>
</section>

<section id="about" class="hidden">
<h2>Savoir plus</h2>
<div class="card">
Ce site permet d’enregistrer, consulter et gérer les informations des patients.<br><br>
<b>Importance :</b><br>
- Centraliser les données médicales<br>
- Faciliter le suivi des malades<br>
- Réduire les pertes d’informations<br>
- Accélérer la prise de décision médicale
</div>
</section>

<section id="patients" class="hidden">
<h2>Gestion des patients</h2>

<div class="card">
<input id="name" placeholder="Nom">
<input id="age" placeholder="Âge">
<input id="illness" placeholder="Maladie">
<button onclick="addPatient()">Ajouter patient</button>
</div>

<div class="card">
<h3>Liste des patients</h3>
<div id="list"></div>
</div>

<div class="card hidden" id="details"></div>
</section>

</div>

<script>

let patients = JSON.parse(localStorage.getItem("patients")||"[]");
let pass = localStorage.getItem("pass");

if(!pass){
document.getElementById("setup").classList.remove("hidden");
}else{
document.getElementById("login").classList.remove("hidden");
}

function savePass(){
let p=document.getElementById("newPass").value;
if(!p)return;
localStorage.setItem("pass",p);
location.reload();
}

function login(){
let p=document.getElementById("pass").value;
if(p===localStorage.getItem("pass")){
document.getElementById("login").classList.add("hidden");
document.getElementById("app").classList.remove("hidden");
render();
}else{
alert("Mot de passe incorrect");
}
}

function show(id){
document.querySelectorAll("section").forEach(s=>s.classList.add("hidden"));
document.getElementById(id).classList.remove("hidden");
}

function addPatient(){
let n=document.getElementById("name").value;
let a=document.getElementById("age").value;
let i=document.getElementById("illness").value;

if(!n||!a||!i)return;

patients.push({id:Date.now(),n,a,i});
localStorage.setItem("patients",JSON.stringify(patients));
render();
}

function render(){
localStorage.setItem("patients",JSON.stringify(patients));

let list=document.getElementById("list");
list.innerHTML="";

patients.forEach(p=>{
list.innerHTML+=`
<div class="patient">
<span onclick="view(${p.id})">${p.n}</span>
<button onclick="del(${p.id})">Supprimer</button>
</div>`;
});
}

function del(id){
patients=patients.filter(p=>p.id!==id);
render();
document.getElementById("details").classList.add("hidden");
}

function view(id){
let p=patients.find(x=>x.id===id);
let d=document.getElementById("details");

d.classList.remove("hidden");
d.innerHTML=`
<h3>Détails patient</h3>
<table>
<tr><td>Nom</td><td>${p.n}</td></tr>
<tr><td>Âge</td><td>${p.a}</td></tr>
<tr><td>Maladie</td><td>${p.i}</td></tr>
</table>
`;
}

function loadImg(e){
let r=new FileReader();
r.onload=function(){
document.getElementById("avatar").src=r.result;
}
r.readAsDataURL(e.target.files[0]);
}

render();

</script>

</body>
</html>