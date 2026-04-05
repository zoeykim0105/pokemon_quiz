<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>포켓몬 암기</title>
  <style>
    body {
      text-align: center;
      font-family: sans-serif;
      background: #f5f5f5;
    }
    img {
      width: 200px;
      margin: 20px;
    }
    input {
      padding: 10px;
      font-size: 18px;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      margin: 10px;
    }
    select {
      padding: 8px;
      font-size: 16px;
    }
  </style>
</head>
<body>

<h1>포켓몬 세대별 암기</h1>

<select id="gen" onchange="newPokemon()">
  <option value="1">1세대 (1~151)</option>
  <option value="2">2세대 (152~251)</option>
  <option value="3">3세대 (252~386)</option>
  <option value="4">4세대 (387~493)</option>
  <option value="5">5세대 (494~649)</option>
  <option value="6">6세대 (650~721)</option>
  <option value="7">7세대 (722~809)</option>
  <option value="8">8세대 (810~905)</option>
  <option value="9">9세대 (906~1010)</option>
</select>

<br>

<img id="img">
<br>

<!-- 🔥 모바일 엔터까지 해결된 입력창 -->
<form onsubmit="submitAnswer(event)">
  <input id="answer" placeholder="이름 입력" onkeydown="handleKey(event)" autofocus>
</form>

<br>

<button onclick="check()">확인</button>

<p id="result"></p>

<script>
let currentId;

const genRange = {
  1: [1,151],
  2: [152,251],
  3: [252,386],
  4: [387,493],
  5: [494,649],
  6: [650,721],
  7: [722,809],
  8: [810,905],
  9: [906,1010]
};

function getRandomId() {
  const gen = document.getElementById("gen").value;
  const [min, max] = genRange[gen];
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function newPokemon() {
  currentId = getRandomId();

  document.getElementById("img").src =
    "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/" + currentId + ".png";

  document.getElementById("answer").value = "";
  document.getElementById("result").innerText = "";

  // 🔥 다음 문제 때 자동 커서
  document.getElementById("answer").focus();
}

// 🔥 컴퓨터 엔터
function handleKey(e) {
  if (e.key === "Enter") {
    check();
  }
}

// 🔥 모바일 엔터 (핵심)
function submitAnswer(e) {
  e.preventDefault();
  check();
}

// 한글 이름 가져오기
async function getKoreanName(id) {
  const res = await fetch("https://pokeapi.co/api/v2/pokemon-species/" + id);
  const data = await res.json();
  const ko = data.names.find(n => n.language.name === "ko");
  return ko.name;
}

async function check() {
  const user = document.getElementById("answer").value.trim();
  const correct = await getKoreanName(currentId);

  if(user === correct) {
    document.getElementById("result").innerText = "정답!";
  } else {
    document.getElementById("result").innerText = "정답: " + correct;
  }

  setTimeout(newPokemon, 1200);
}

newPokemon();
</script>

</body>
</html>
