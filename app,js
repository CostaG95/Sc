// app.js - Backend em Node.js

const express = require('express');
const bodyParser = require('body-parser');
const crypto = require('crypto');

const app = express();
const port = process.env.PORT || 3000;

// Middleware
app.use(bodyParser.json());
app.use(express.static('public'));

// Dados simulados em memória
let participantes = [];
let sorteio = {};

// Rota para adicionar participantes
app.post('/add', (req, res) => {
    const { nome } = req.body;
    if (!nome || participantes.includes(nome)) {
        return res.status(400).json({ error: 'Nome inválido ou já existe.' });
    }
    participantes.push(nome);
    res.json({ message: 'Participante adicionado com sucesso!', participantes });
});

// Rota para realizar o sorteio
app.post('/sorteio', (req, res) => {
    if (participantes.length < 2) {
        return res.status(400).json({ error: 'Mínimo de 2 participantes necessário.' });
    }

    let shuffled = [...participantes];
    do {
        shuffled = shuffled.sort(() => Math.random() - 0.5);
    } while (shuffled.some((p, i) => p === participantes[i]));

    sorteio = participantes.reduce((acc, p, i) => {
        const token = crypto.randomBytes(4).toString('hex');
        acc[p] = { amigo: shuffled[i], token };
        return acc;
    }, {});

    res.json({ message: 'Sorteio realizado com sucesso!', sorteio });
});

// Rota para ver o resultado
app.get('/resultado/:token', (req, res) => {
    const { token } = req.params;
    const participante = Object.entries(sorteio).find(
        ([, value]) => value.token === token
    );

    if (!participante) {
        return res.status(404).json({ error: 'Token inválido.' });
    }

    res.json({ amigo: participante[1].amigo });
});

// Iniciar o servidor
app.listen(port, () => {
    console.log(`Servidor iniciado em http://localhost:${port}`);
});

/* HTML, CSS e Frontend */
// public/index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Amigo Secreto</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>Amigo Secreto</h1>
        <form id="add-form">
            <input type="text" id="nome" placeholder="Digite o nome" required>
            <button type="submit">Adicionar Participante</button>
        </form>
        <ul id="participantes-list"></ul>
        <button id="sorteio-btn">Realizar Sorteio</button>
        <div id="result" class="hidden">
            <h2>Resultado</h2>
            <p id="amigo-secreto"></p>
        </div>
    </div>
    <script src="scripts.js"></script>
</body>
</html>

// public/styles.css
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f9;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
}
.container {
    text-align: center;
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
.hidden {
    display: none;
}

// public/scripts.js
const form = document.getElementById('add-form');
const nomeInput = document.getElementById('nome');
const participantesList = document.getElementById('participantes-list');
const sorteioBtn = document.getElementById('sorteio-btn');
const resultDiv = document.getElementById('result');
const amigoSecretoP = document.getElementById('amigo-secreto');

form.addEventListener('submit', async (e) => {
    e.preventDefault();
    const nome = nomeInput.value.trim();
    if (nome) {
        const response = await fetch('/add', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ nome })
        });
        const data = await response.json();
        if (data.error) {
            alert(data.error);
        } else {
            nomeInput.value = '';
            participantesList.innerHTML = data.participantes.map(p => `<li>${p}</li>`).join('');
        }
    }
});

sorteioBtn.addEventListener('click', async () => {
    const response = await fetch('/sorteio', { method: 'POST' });
    const data = await response.json();
    if (data.error) {
        alert(data.error);
    } else {
        alert('Sorteio realizado! Cada participante pode acessar seu link único para ver o amigo secreto.');
    }
});
