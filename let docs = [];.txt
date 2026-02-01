let docs = [];

function openTab(name) {
  document.querySelectorAll('.tabcontent').forEach(c => c.classList.remove('active'));
  document.getElementById(name).classList.add('active');
}

function readFiles() {
  const files = document.getElementById('fileInput').files;
  docs = [];
  Array.from(files).forEach(file => {
    const reader = new FileReader();
    reader.onload = () => {
      docs.push({ name: file.name, text: reader.result });
      document.getElementById('uploadStatus').innerHTML += `<p>📄 ${file.name} geladen.</p>`;
    };
    reader.readAsText(file);
  });
}

async function makeAISummary() {
  if (docs.length === 0) return alert("Upload eerst bestanden!");
  document.getElementById('summaryOutput').innerHTML = "⏳ Samenvatting genereren...";

  const response = await fetch('/api/summarize', {
    method:'POST',
    headers: {'Content-Type':'application/json'},
    body: JSON.stringify({ docs })
  });
  const result = await response.json();
  document.getElementById('summaryOutput').innerHTML = `<pre>${result.summary}</pre>`;
}

function makeQuiz() {
  let out = "";
  docs.forEach(d => out += `<p>❓ Vraag bij ${d.name}: ...</p>`);
  document.getElementById('quizOutput').innerHTML = out;
}

function createPlan() {
  const deadline = new Date(document.getElementById('deadline').value);
  const days = document.getElementById('days').value.split(',').map(Number);
  let cur = new Date(), plan="";
  let i=0;
  while(cur <= deadline && i < docs.length) {
    if(days.includes(cur.getDay())) {
      plan += `<div>${cur.toDateString()}: leer ${docs[i].name}</div>`;
      i++;
    }
    cur.setDate(cur.getDate()+1);
  }
  document.getElementById('planOutput').innerHTML = plan;
}
