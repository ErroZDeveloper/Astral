<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Quiz de Astronomia Aleatório</title>
<style>
body { font-family: Arial, sans-serif; background: linear-gradient(to right,#0f2027,#203a43,#2c5364); color:#fff; display:flex; justify-content:center; align-items:center; min-height:100vh; margin:0;}
#quizContainer { background: rgba(255,255,255,0.05); padding:30px; border-radius:20px; width:800px; max-width:95%; box-shadow:0 8px 20px rgba(0,0,0,0.5);}
h1 { text-align:center; color:#ffdd59; margin-bottom:20px;}
button { padding:10px 20px; margin-top:10px; cursor:pointer; border:none; border-radius:8px; background:#ffdd59; color:#222; font-weight:bold; transition:0.3s; }
button:hover { background:#ffd633; }
#options button { display:block; width:100%; margin:8px 0; text-align:left; }
input[type="text"] { padding:10px; width:100%; border-radius:8px; border:none; margin-top:5px; }
textarea { width:100%; height:200px; margin-top:10px; border-radius:8px; border:none; padding:10px; }
#progress { font-weight:bold; }
</style>
</head>
<body>
<div id="quizContainer">
<h1>Quiz de Astronomia 🌌</h1>

<div id="nameInputDiv">
<p>Digite seu nome:</p>
<input type="text" id="playerName" placeholder="Seu nome">
<button onclick="startQuiz()">Começar</button>
</div>

<div id="questionDiv" style="display:none;">
<h2 id="questionText"></h2>
<div id="options"></div>
<p id="progress" style="text-align:center;margin-top:10px;"></p>
</div>

<div id="resultDiv" style="display:none;">
<h2>Resultados</h2>
<p id="scoreText"></p>
<p id="wrongText"></p>
<h3>Gabarito completo:</h3>
<textarea id="answerKey" readonly></textarea>
<button onclick="downloadResults()">Baixar Resultados</button>
</div>

<script>
// =======================
// Banco de 100 perguntas únicas baseadas nos tópicos fornecidos
const bank = [
{"q":"Qual é o planeta mais próximo do Sol?","options":["Mercúrio","Vênus","Terra","Marte"],"answer":"Mercúrio"},
{"q":"Qual planeta anão foi reclassificado em 2006 pela IAU?","options":["Plutão","Ceres","Eris","Haumea"],"answer":"Plutão"},
{"q":"Qual exoplaneta descoberto orbitando Proxima Centauri é potencialmente habitável?","options":["Proxima b","Kepler-22b","TRAPPIST-1d","HD 209458 b"],"answer":"Proxima b"},
{"q":"Qual é o maior asteroide do cinturão principal?","options":["Ceres","Vesta","Pallas","Hygiea"],"answer":"Ceres"},
{"q":"Qual cometa tem uma órbita de aproximadamente 76 anos e é muito famoso?","options":["Halley","Hale-Bopp","Encke","Tempel 1"],"answer":"Halley"},
{"q":"O que são meteoros?","options":["Estrelas cadentes","Fragmentos de asteroides","Planetas pequenos","Satélites naturais"],"answer":"Estrelas cadentes"},
{"q":"O que é um meteorito?","options":["Fragmento que chega à Terra","Cometa que passa perto","Planeta anão","Asteroide em órbita"],"answer":"Fragmento que chega à Terra"},
{"q":"Qual é a estrela mais próxima além do Sol?","options":["Proxima Centauri","Sirius","Betelgeuse","Vega"],"answer":"Proxima Centauri"},
{"q":"O que são estrelas binárias?","options":["Duas estrelas orbitando-se","Estrelas duplas colapsadas","Estrelas com planetas","Estrelas variáveis"],"answer":"Duas estrelas orbitando-se"},
{"q":"O que são estrelas variáveis?","options":["Estrelas que mudam de brilho","Estrelas com planetas","Supernovas","Estrelas antigas"],"answer":"Estrelas que mudam de brilho"},
{"q":"O que é uma anã branca?","options":["Estrela em estágio final","Estrela jovem","Buraco negro","Planeta"],"answer":"Estrela em estágio final"},
{"q":"O que é uma anã vermelha?","options":["Estrela pequena e fria","Estrela gigante","Supernova","Cometa"],"answer":"Estrela pequena e fria"},
{"q":"O que são gigantes vermelhas?","options":["Estrelas envelhecidas e grandes","Estrelas jovens","Estrelas binárias","Asteroides"],"answer":"Estrelas envelhecidas e grandes"},
{"q":"O que são gigantes azuis?","options":["Estrelas muito massivas e quentes","Planetas gasosos","Anãs brancas","Buracos negros"],"answer":"Estrelas muito massivas e quentes"},
{"q":"O que são supergigantes?","options":["Estrelas enormes e luminosas","Estrelas médias","Planetas anões","Asteroides"],"answer":"Estrelas enormes e luminosas"},
{"q":"O que é um buraco negro?","options":["Objeto com gravidade extrema","Estrela gigante","Galáxia pequena","Planeta gasoso"],"answer":"Objeto com gravidade extrema"},
{"q":"O que é um buraco de verme?","options":["Atalho no espaço-tempo","Estrela variável","Exoplaneta","Cometa"],"answer":"Atalho no espaço-tempo"},
{"q":"O que são quasares?","options":["Núcleos galácticos ativos","Estrelas jovens","Buracos brancos","Planetas massivos"],"answer":"Núcleos galácticos ativos"},
{"q":"O que são pulsars?","options":["Estrelas de nêutrons girando rapidamente","Estrelas binárias","Cometas","Aglomerados estelares"],"answer":"Estrelas de nêutrons girando rapidamente"},
{"q":"O que são nebulosas?","options":["Nuvens de gás e poeira no espaço","Planetas gasosos","Estrelas velhas","Exoplanetas"],"answer":"Nuvens de gás e poeira no espaço"},
{"q":"Qual é a nebulosa de Órion?","options":["M42","M31","M13","M1"],"answer":"M42"},
{"q":"Qual é a nebulosa de Carina?","options":["NGC 3372","M42","M13","NGC 1976"],"answer":"NGC 3372"},
{"q":"Qual é a nebulosa Trífida?","options":["M20","M42","NGC 1976","M31"],"answer":"M20"},
{"q":"Qual galáxia contém o Sistema Solar?","options":["Via Láctea","Andrômeda","Triângulo","Sombrero"],"answer":"Via Láctea"},
{"q":"Qual galáxia vizinha mais próxima da Via Láctea?","options":["Andrômeda","Triângulo","Sombrero","Círculo"],"answer":"Andrômeda"},
{"q":"Qual é a galáxia do Triângulo?","options":["M33","M31","M87","NGC 1300"],"answer":"M33"},
{"q":"Qual é a galáxia do Sombrero?","options":["M104","M31","M33","M87"],"answer":"M104"},
{"q":"O que é radiação cósmica de fundo?","options":["Radiação remanescente do Big Bang","Luz de estrelas próximas","Raios X","Radiação de quasares"],"answer":"Radiação remanescente do Big Bang"},
{"q":"O que é matéria escura?","options":["Matéria invisível que influencia gravidade","Planetas não visíveis","Cometas distantes","Estrelas apagadas"],"answer":"Matéria invisível que influencia gravidade"},
{"q":"O que é energia escura?","options":["Força que acelera a expansão do universo","Luz de estrelas distantes","Gravidade de galáxias","Raios cósmicos"],"answer":"Força que acelera a expansão do universo"},
{"q":"O que são exosferas?","options":["Camada externa da atmosfera de planetas","Superfície de estrelas","Galáxias próximas","Aglomerados de estrelas"],"answer":"Camada externa da atmosfera de planetas"},
{"q":"O que são telescópios espaciais?","options":["Instrumentos no espaço para observar o universo","Telescópios terrestres","Satélites de comunicação","Cometas artificiais"],"answer":"Instrumentos no espaço para observar o universo"},
{"q":"O que são missões espaciais?","options":["Exploração do espaço por sondas ou humanos","Estudos de meteoritos","Observação de cometas","Aglomerados globulares"],"answer":"Exploração do espaço por sondas ou humanos"},
{"q":"O que são órbitas elípticas?","options":["Órbitas em forma de elipse","Órbitas circulares perfeitas","Trajetórias de cometas","Movimentos de galáxias"],"answer":"Órbitas em forma de elipse"},
{"q":"O que é o cinturão de asteroides?","options":["Região entre Marte e Júpiter com asteroides","Região do Sistema Solar externo","Órbita da Terra","Nuvem de Oort"],"answer":"Região entre Marte e Júpiter com asteroides"},
{"q":"O que é o cinturão de Kuiper?","options":["Região além de Netuno com objetos gelados","Cinturão de planetas","Órbita do Sol","Estrelas próximas"],"answer":"Região além de Netuno com objetos gelados"},
{"q":"O que é a Nuvem de Oort?","options":["Região distante com cometas","Galáxia próxima","Cinturão de asteroides","Planeta anão"],"answer":"Região distante com cometas"},
{"q":"O que são atmosferas planetárias?","options":["Camadas gasosas ao redor dos planetas","Superfícies planetárias","Núcleos planetários","Cinturões de cometas"],"answer":"Camadas gasosas ao redor dos planetas"},
{"q":"O que é rotação planetária?","options":["Movimento em torno do próprio eixo","Movimento em torno do Sol","Movimento de satélites","Trajetória de meteoros"],"answer":"Movimento em torno do próprio eixo"},
{"q":"O que é translação planetária?","options":["Movimento em torno do Sol","Movimento em torno do eixo","Movimento de estrelas","Formação de galáxias"],"answer":"Movimento em torno do Sol"},
{"q":"O que são eclipses solares?","options":["Lua entre Sol e Terra","Terra entre Sol e Lua","Sol escondido por planeta","Cometa entre Sol e Terra"],"answer":"Lua entre Sol e Terra"},
{"q":"O que são eclipses lunares?","options":["Terra entre Sol e Lua","Lua entre Sol e Terra","Terra entre Lua e Marte","Lua escondida por planeta"],"answer":"Terra entre Sol e Lua"},
{"q":"O que são marés?","options":["Variação do nível do mar causada pela Lua e Sol","Ondas normais","Correntes oceânicas","Tempestades"],"answer":"Variação do nível do mar causada pela Lua e Sol"},
{"q":"O que é aurora boreal?","options":["Luzes no céu do hemisfério norte","Luzes no céu do hemisfério sul","Cometa brilhante","Explosão solar"],"answer":"Luzes no céu do hemisfério norte"},
{"q":"O que é aurora austral?","options":["Luzes no céu do hemisfério sul","Luzes no céu do hemisfério norte","Cometa brilhante","Explosão solar"],"answer":"Luzes no céu do hemisfério sul"},
{"q":"O que são planetas gasosos?","options":["Júpiter, Saturno, Urano, Netuno","Mercúrio e Vênus","Marte e Terra","Plutão e Ceres"],"answer":"Júpiter, Saturno, Urano, Netuno"},
{"q":"O que são planetas rochosos?","options":["Mercúrio, Vênus, Terra, Marte","Júpiter e Saturno","Urano e Netuno","Plutão e Haumea"],"answer":"Mercúrio, Vênus, Terra, Marte"},
{"q":"O que são eventos de trânsito planetário?","options":["Planeta passa na frente da estrela","Planeta se move rápido","Cometa cruzando o Sol","Eclipse lunar"],"answer":"Planeta passa na frente da estrela"},
{"q":"O que são estrelas supernovas?","options":["Estrelas que explodem no fim da vida","Estrelas jovens","Estrelas binárias","Anãs brancas"],"answer":"Estrelas que explodem no fim da vida"},
{"q":"O que é supernova tipo Ia?","options":["Explosão de anã branca em sistema binário","Explosão de gigante azul","Explosão de estrela jovem","Explosão de estrela de nêutrons"],"answer":"Explosão de anã branca em sistema binário"},
{"q":"O que é supernova tipo II?","options":["Explosão de estrela massiva","Explosão de anã branca","Explosão de estrela binária","Explosão de planeta"],"answer":"Explosão de estrela massiva"},
{"q":"O que são estrelas de nêutrons?","options":["Resíduo compacto de supernova","Estrela gigante","Planeta gasoso","Cometa"],"answer":"Resíduo compacto de supernova"},
{"q":"O que são buracos negros estelares?","options":["Formados a partir de estrelas massivas","Pequenos planetas","Estrelas binárias","Estrelas jovens"],"answer":"Formados a partir de estrelas massivas"},
{"q":"O que são buracos negros supermassivos?","options":["No centro de galáxias","Pequenas estrelas","Planetas gigantes","Cometas"],"answer":"No centro de galáxias"},
{"q":"O que são aglomerados estelares abertos?","options":["Estrelas jovens agrupadas","Estrelas isoladas","Buracos negros","Cometas"],"answer":"Estrelas jovens agrupadas"},
{"q":"O que são aglomerados globulares?","options":["Estrelas antigas e densas","Estrelas novas","Planetas","Nebulosas"],"answer":"Estrelas antigas e densas"},
{"q":"O que são anéis planetários?","options":["Estruturas em torno de planetas gasosos","Anéis de satélites","Asteroides","Galáxias"],"answer":"Estruturas em torno de planetas gasosos"},
{"q":"O que é magnetosfera?","options":["Campo magnético ao redor de planetas","Campo magnético de estrelas","Buraco negro","Planeta gasoso"],"answer":"Campo magnético ao redor de planetas"},
{"q":"O que é cromosfera?","options":["Camada da atmosfera solar","Núcleo da Terra","Planeta anão","Buraco negro"],"answer":"Camada da atmosfera solar"},
{"q":"O que é fotosfera?","options":["Superfície visível do Sol","Camada de Marte","Estrela de nêutrons","Nebulosa"],"answer":"Superfície visível do Sol"},
{"q":"O que é coroa solar?","options":["Camada externa do Sol","Camada interna da Terra","Buraco negro","Galáxia"],"answer":"Camada externa do Sol"},
{"q":"O que são protostrelas?","options":["Estrelas em formação","Estrelas antigas","Planetas gasosos","Buracos negros"],"answer":"Estrelas em formação"},
{"q":"O que são estrelas T Tauri?","options":["Estrelas jovens variáveis","Estrelas antigas","Supernovas","Buracos negros"],"answer":"Estrelas jovens variáveis"},
{"q":"O que são estrelas Herbig Ae/Be?","options":["Estrelas jovens massivas","Estrelas velhas","Planetas gasosos","Buracos negros"],"answer":"Estrelas jovens massivas"},
{"q":"O que são regiões H II?","options":["Nuvens ionizadas de hidrogênio","Planetas gasosos","Estrelas antigas","Cometas"],"answer":"Nuvens ionizadas de hidrogênio"},
{"q":"O que são nebulosas planetárias?","options":["Gás expelido por estrelas no fim da vida","Planetas em formação","Cometas","Buracos negros"],"answer":"Gás expelido por estrelas no fim da vida"},
{"q":"O que são nebulosas de reflexão?","options":["Refletem luz de estrelas próximas","Emitam radiação própria","Planetas gasosos","Buracos negros"],"answer":"Refletem luz de estrelas próximas"},
{"q":"O que são nebulosas de emissão?","options":["Emitem luz própria por ionização","Refletem luz de outras estrelas","Estrelas jovens","Cometas"],"answer":"Emitem luz própria por ionização"},
{"q":"O que são nebulosas escuras?","options":["Nuvens que bloqueiam a luz de fundo","Emitem radiação X","Estrelas velhas","Planetas"],"answer":"Nuvens que bloqueiam a luz de fundo"},
{"q":"O que são estrelas hipergigantes?","options":["Estrelas extremamente massivas e luminosas","Estrelas pequenas","Planetas gasosos","Buracos negros"],"answer":"Estrelas extremamente massivas e luminosas"},
{"q":"O que são estrelas Wolf-Rayet?","options":["Estrelas massivas com fortes ventos estelares","Estrelas pequenas","Planetas","Nebulosas"],"answer":"Estrelas massivas com fortes ventos estelares"},
{"q":"O que é lente gravitacional?","options":["Desvio da luz por gravidade","Refração atmosférica","Buraco negro","Estrela gigante"],"answer":"Desvio da luz por gravidade"},
{"q":"O que são microlentes gravitacionais?","options":["Pequeno desvio de luz por objetos massivos","Refração atmosférica","Estrelas velhas","Planetas"],"answer":"Pequeno desvio de luz por objetos massivos"},
{"q":"O que são quasares de rádio?","options":["Quasares que emitem fortes ondas de rádio","Estrelas jovens","Buracos negros pequenos","Planetas gasosos"],"answer":"Quasares que emitem fortes ondas de rádio"},
{"q":"O que são galáxias elípticas?","options":["Galáxias com formato elíptico","Galáxias espirais","Galáxias irregulares","Galáxias lenticulares"],"answer":"Galáxias com formato elíptico"},
{"q":"O que são galáxias espirais?","options":["Galáxias em forma de disco com braços","Galáxias elípticas","Irregulares","Lenticulares"],"answer":"Galáxias em forma de disco com braços"},
{"q":"O que são galáxias irregulares?","options":["Galáxias sem forma definida","Elípticas","Espirais","Lenticulares"],"answer":"Galáxias sem forma definida"},
{"q":"O que são galáxias lenticulares?","options":["Galáxias intermediárias entre espirais e elípticas","Espirais","Elípticas","Irregulares"],"answer":"Galáxias intermediárias entre espirais e elípticas"},
{"q":"O que são filamentos cósmicos?","options":["Grandes estruturas de matéria no universo","Estrelas jovens","Planetas gasosos","Buracos negros"],"answer":"Grandes estruturas de matéria no universo"},
{"q":"O que é vácuo intergaláctico?","options":["Região quase sem matéria entre galáxias","Galáxias distantes","Nebulosas","Planetas"],"answer":"Região quase sem matéria entre galáxias"},
{"q":"O que é radiação ultravioleta?","options":["Luz invisível de alta energia","Luz visível","Raios X","Radiação gama"],"answer":"Luz invisível de alta energia"},
{"q":"O que é radiação infravermelha?","options":["Radiação térmica","Luz visível","Raios X","Radiação gama"],"answer":"Radiação térmica"},
{"q":"O que é radiação X?","options":["Radiação de alta energia","Luz visível","Infravermelho","Ultravioleta"],"answer":"Radiação de alta energia"},
{"q":"O que é radiação gama?","options":["Radiação mais energética","Radiação de baixa energia","Luz visível","Infravermelho"],"answer":"Radiação mais energética"},
{"q":"O que são jets relativísticos?","options":["Fluxos de partículas próximos à velocidade da luz","Ventos solares","Planetas gasosos","Buracos negros pequenos"],"answer":"Fluxos de partículas próximos à velocidade da luz"},
{"q":"O que são magnetars?","options":["Estrelas de nêutrons com campo magnético extremo","Planetas gasosos","Estrelas jovens","Cometas"],"answer":"Estrelas de nêutrons com campo magnético extremo"},
{"q":"O que são raios cósmicos?","options":["Partículas de alta energia vindas do espaço","Luz visível","Raios X","Ondas de rádio"],"answer":"Partículas de alta energia vindas do espaço"},
{"q":"O que são buracos brancos (hipotéticos)?","options":["Objetos teóricos que expulsam matéria","Buracos negros","Estrelas jovens","Planetas"],"answer":"Objetos teóricos que expulsam matéria"},
{"q":"O que é matéria bariônica?","options":["Matéria comum composta por prótons e nêutrons","Matéria escura","Energia escura","Radiação cósmica"],"answer":"Matéria comum composta por prótons e nêutrons"},
{"q":"O que é matéria não bariônica?","options":["Matéria escura","Matéria comum","Planetas","Estrelas"],"answer":"Matéria escura"},
{"q":"O que são protuberâncias solares?","options":["Erupções de plasma do Sol","Manchas solares","Cometas","Planetas"],"answer":"Erupções de plasma do Sol"},
{"q":"O que são manchas solares?","options":["Áreas escuras na fotosfera do Sol","Erupções de plasma","Buracos negros","Galáxias"],"answer":"Áreas escuras na fotosfera do Sol"},
{"q":"O que é ciclo solar?","options":["Período de atividade do Sol de cerca de 11 anos","Rotação da Terra","Órbita de Júpiter","Translação da Lua"],"answer":"Período de atividade do Sol de cerca de 11 anos"},
{"q":"O que é pó interestelar?","options":["Partículas de poeira no espaço","Cometa","Planeta","Estrela"],"answer":"Partículas de poeira no espaço"},
{"q":"O que é gás intergaláctico?","options":["Gás entre galáxias","Gás em planetas","Gás em estrelas","Gás em buracos negros"],"answer":"Gás entre galáxias"},
{"q":"O que é disco de acreção?","options":["Disco de matéria em torno de buracos negros","Planeta gasoso","Estrela jovem","Cometa"],"answer":"Disco de matéria em torno de buracos negros"},
{"q":"O que são fusões de galáxias?","options":["Quando duas galáxias se unem","Colisão de estrelas","Formação de planetas","Explosão de supernova"],"answer":"Quando duas galáxias se unem"},
{"q":"O que é singularidade gravitacional?","options":["Ponto de densidade infinita em buracos negros","Estrela gigante","Planeta denso","Nebulosa"],"answer":"Ponto de densidade infinita em buracos negros"},
{"q":"O que é cosmologia?","options":["Estudo do universo como um todo","Estudo de planetas","Estudo de estrelas jovens","Estudo de meteoritos"],"answer":"Estudo do universo como um todo"}
];

// =======================
// Variables
let questions = [];
let currentQuestion = 0;
let score = 0;
let wrongAnswers = [];
let playerName = "";

// =======================
function startQuiz(){
    playerName = document.getElementById("playerName").value.trim();
    if(playerName===""){ alert("Digite seu nome"); return; }
    document.getElementById("nameInputDiv").style.display="none";
    document.getElementById("questionDiv").style.display="block";
    
    // Escolher 50 perguntas aleatórias
    questions = bank.sort(()=>0.5-Math.random()).slice(0,50);
    questions.forEach(q=>q.options = q.options.sort(()=>0.5-Math.random()));
    currentQuestion=0; score=0; wrongAnswers=[];
    showQuestion();
}

// =======================
function showQuestion(){
    if(currentQuestion>=questions.length){
        showResult();
        return;
    }
    const q = questions[currentQuestion];
    document.getElementById("questionText").innerText = q.q;
    const optionsDiv = document.getElementById("options");
    optionsDiv.innerHTML="";
    q.options.forEach(opt=>{
        const btn = document.createElement("button");
        btn.innerText=opt;
        btn.onclick = ()=>checkAnswer(opt);
        optionsDiv.appendChild(btn);
    });
    document.getElementById("progress").innerText = `Pergunta ${currentQuestion+1} de ${questions.length}`;
}

// =======================
function checkAnswer(opt){
    const q = questions[currentQuestion];
    if(opt===q.answer) score++;
    else wrongAnswers.push({num:currentQuestion+1, correct:q.answer});
    currentQuestion++;
    showQuestion();
}

// =======================
function showResult(){
    document.getElementById("questionDiv").style.display="none";
    document.getElementById("resultDiv").style.display="block";
    document.getElementById("scoreText").innerText = `Acertos: ${score}`;
    document.getElementById("wrongText").innerText = `Erros: ${wrongAnswers.length}`;
    
    let gabarito = "";
    questions.forEach((q,i)=> gabarito+=`${i+1} - ${q.answer}\n`);
    document.getElementById("answerKey").value = gabarito;
}

// =======================
function downloadResults(){
    let text = `Nome: ${playerName}\nAcertos: ${score}\nErros: ${wrongAnswers.length}\nErros Detalhados:\n`;
    wrongAnswers.forEach(w=>text+=`Questão ${w.num} - Resposta correta: ${w.correct}\n`);
    text+="\nGabarito completo:\n";
    questions.forEach((q,i)=> text+=`${i+1} - ${q.answer}\n`);
    
    const blob = new Blob([text], {type:"text/plain"});
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = `Resultados_${playerName}.txt`;
    a.click();
    URL.revokeObjectURL(url);
}
</script>
</body>
</html>
