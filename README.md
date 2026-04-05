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

<h1>포켓몬 암기 (순서 + 복습)</h1>

<select id="gen" onchange="startGame()">
  <option value="1">1세대</option>
  <option value="2">2세대</option>
  <option value="3">3세대</option>
  <option value="4">4세대</option>
  <option value="5">5세대</option>
  <option value="6">6세대</option>
  <option value="7">7세대</option>
  <option value="8">8세대</option>
  <option value="9">9세대</option>
</select>

<br>

<img id="img">
<br>

<form onsubmit="submitAnswer(event)">
  <input id="answer" placeholder="이름 입력" autofocus>
</form>

<p id="result"></p>
<p id="progress"></p>

<script>
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

let list = [];
let wrong = [];
let index = 0;
let currentId;

function startGame() {
  const gen = document.getElementById("gen").value;
  const [min, max] = genRange[gen];

  list = [];
  for(let i=min; i<=max; i++) list.push(i);

  wrong = [];
  index = 0;

  next();
}

function next() {
  if(index >= list.length) {
    if(wrong.length > 0) {
      list = [...wrong];
      wrong = [];
      index = 0;
      alert("오답 복습 시작!");
    } else {
      document.getElementById("result").innerText = "🎉 다 외웠다!";
      return;
    }
  }

  currentId = list[index];

  document.getElementById("img").src =
    "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/" + currentId + ".png";

  document.getElementById("answer").value = "";
  document.getElementById("result").innerText = "";
  document.getElementById("progress").innerText =
    (index+1) + " / " + list.length;

  document.getElementById("answer").focus();
}

function submitAnswer(e) {
  e.preventDefault();
  check();
}

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
    wrong.push(currentId);
  }

  index++;
  setTimeout(next, 800);
}

// 처음 시작
startGame();
</script>

</body>
</html>
