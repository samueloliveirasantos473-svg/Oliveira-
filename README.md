<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Minhas Tarefas</title>

<style>
:root {
    --bg: #eceff1;
    --card: #ffffff;
    --text: #111;
}

body {
    font-family: Arial;
    background: var(--bg);
    color: var(--text);
    padding: 20px;
}

h1, #data, #contador {
    text-align: center;
}

.progress {
    height: 10px;
    background: #cfd8dc;
    border-radius: 10px;
    overflow: hidden;
    margin-bottom: 20px;
}

.bar {
    height: 100%;
    width: 0%;
    background: linear-gradient(90deg, #000, #444);
    transition: 0.4s;
}

.top {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.top button {
    flex: 1;
    padding: 12px;
    border-radius: 10px;
    border: none;
    background: #000;
    color: #fff;
    cursor: pointer;
    font-weight: bold;
}

.input-container {
    display: flex;
    gap: 5px;
    margin-bottom: 20px;
}

input, select {
    padding: 10px;
    border-radius: 8px;
    border: 1px solid #999;
}

.bloco {
    border-radius: 14px;
    padding: 12px;
    margin-bottom: 15px;
    background: #000;
    color: #fff;
}

ul {
    list-style: none;
    padding: 0;
}

.bloco li {
    background: #fff;
    color: #000;
    padding: 10px;
    margin-top: 6px;
    border-radius: 8px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.info {
    display: flex;
    flex-direction: column;
}

.hora {
    font-size: 12px;
    color: #666;
}

.concluida {
    text-decoration: line-through;
    opacity: 0.5;
}

.actions button {
    margin-left: 5px;
}
</style>
</head>

<body>

<h1>Minhas Tarefas</h1>
<p id="data"></p>
<p id="contador"></p>

<div class="progress"><div class="bar" id="bar"></div></div>

<div class="top">
    <button onclick="concluirTudo()">Concluir todas</button>
    <button onclick="limpar()">Limpar tudo</button>
</div>

<div class="input-container">
    <input id="tarefa" placeholder="Digite uma tarefa...">
    <input type="time" id="hora">
    <select id="periodo">
        <option value="manha">Manhã</option>
        <option value="tarde">Tarde</option>
        <option value="noite">Noite</option>
    </select>
    <button onclick="add()">Adicionar</button>
</div>

<div class="bloco"><h3>Manhã</h3><ul id="manha"></ul></div>
<div class="bloco"><h3>Tarde</h3><ul id="tarde"></ul></div>
<div class="bloco"><h3>Noite</h3><ul id="noite"></ul></div>

<script>

function criar(t,p,c,h){
    let li=document.createElement("li");

    let info=document.createElement("div");
    info.className="info";

    let span=document.createElement("span");
    span.innerText=t;

    let hora=document.createElement("span");
    hora.className="hora";
    hora.innerText=h || "";

    if(c) span.classList.add("concluida");

    let actions=document.createElement("div");
    actions.className="actions";

    let btnCheck=document.createElement("button");
    btnCheck.innerText="✔";
    btnCheck.onclick=()=>{
        span.classList.toggle("concluida");
        salvar(); atualizar();
    };

    let btnDel=document.createElement("button");
    btnDel.innerText="❌";
    btnDel.onclick=()=>{
        li.remove();
        salvar(); atualizar();
    };

    info.append(span,hora);
    actions.append(btnCheck,btnDel);

    li.append(info,actions);
    document.getElementById(p).appendChild(li);
}

function add(){
    let t=tarefa.value.trim();
    let h=hora.value;

    if(!t) return;

    criar(t,periodo.value,false,h);

    tarefa.value="";
    hora.value="";
    salvar(); atualizar();
}

function salvar(){
    let dados=[];
    ["manha","tarde","noite"].forEach(p=>{
        document.getElementById(p).querySelectorAll("li").forEach(li=>{
            dados.push({
                t: li.querySelector(".info span").innerText,
                p,
                c: li.querySelector(".info span").classList.contains("concluida"),
                h: li.querySelector(".hora").innerText
            });
        });
    });
    localStorage.setItem("tarefas",JSON.stringify(dados));
}

function carregar(){
    let d=JSON.parse(localStorage.getItem("tarefas"))||[];
    d.forEach(x=>criar(x.t,x.p,x.c,x.h));
}

function atualizar(){
    let total=document.querySelectorAll("li").length;
    let done=document.querySelectorAll(".concluida").length;

    contador.innerText = total
    ? `${total-done} pendentes | ${done} concluídas`
    : "Nenhuma tarefa cadastrada.";

    let p= total? (done/total)*100:0;
    bar.style.width=p+"%";
}

function concluirTudo(){
    document.querySelectorAll("li span").forEach(s=>{
        s.classList.add("concluida");
    });
    salvar(); atualizar();
}

function limpar(){
    if(confirm("Deseja apagar tudo?")){
        ["manha","tarde","noite"].forEach(p=>{
            document.getElementById(p).innerHTML="";
        });
        localStorage.removeItem("tarefas");
        atualizar();
    }
}

data.innerText=new Date().toLocaleDateString("pt-BR");

window.onload=()=>{
    carregar();
    atualizar();
};

</script>

</body>
</html>
