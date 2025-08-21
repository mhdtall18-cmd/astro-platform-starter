# Astro on Netlify Platform Starter

[Live Demo](https://astro-platform-starter.netlify.app/)

A modern starter based on Astro.js, Tailwind, and [Netlify Core Primitives](https://docs.netlify.com/core/overview/#develop) (Edge Functions, Image CDN, Blob Store).

## Astro Commands

All commands are run from the root of the project, from a terminal:

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `npm install`             | Installs dependencies                            |
| `npm run dev`             | Starts local dev server at `localhost:4321`      |
| `npm run build`           | Build your production site to `./dist/`          |
| `npm run preview`         | Preview your build locally, before deploying     |
| `npm run astro ...`       | Run CLI commands like `astro add`, `astro check` |
| `npm run astro -- --help` | Get help using the Astro CLI                     |

## Deploying to Netlify

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/netlify-templates/astro-platform-starter)

## Developing Locally

| Prerequisites                                                                |
| :--------------------------------------------------------------------------- |
| [Node.js](https://nodejs.org/) v18.14+.                                      |
| (optional) [nvm](https://github.com/nvm-sh/nvm) for Node version management. |

1. Clone this repository, then run `npm install` in its root directory.

2. For the starter to have full functionality locally (e.g. edge functions, blob store), please ensure you have an up-to-date version of Netlify CLI. Run:

```
npm install netlify-cli@latest -g
```

3. Link your local repository to the deployed Netlify site. This will ensure you're using the same runtime version for both local development and your deployed site.

```
netlify link
```

4. Then, run the Astro.js development server via Netlify CLI:

```
netlify dev
```

If your browser doesn't navigate to the site automatically, visit [localhost:8888](http://localhost:8888).
const matches = [
    { id: 1, team1: "PSG", team2: "OM", odds: { "PSG": 1.8, "OM": 2.1 } },
    { id: 2, team1: "Real Madrid", team2: "Barça", odds: { "Real Madrid": 1.9, "Barça": 2.0 } },
    { id: 3, team1: "Bayern", team2: "Dortmund", odds: { "Bayern": 1.7, "Dortmund": 2.3 } }
];

let bets = [];
let stats = { wins: 0, losses: 0 };

const matchesDiv = document.getElementById("matches");
const betsDiv = document.getElementById("bets");
const simulateBtn = document.getElementById("simulate-btn");

function renderMatches() {
    matchesDiv.innerHTML = "";
    matches.forEach(match => {
        const matchCard = document.createElement("div");
        matchCard.className = "match-card";
        matchCard.innerHTML = `
            <p><strong>${match.team1}</strong> vs <strong>${match.team2}</strong></p>
            <p>Cotes: ${match.team1} (${match.odds[match.team1]}) | ${match.team2} (${match.odds[match.team2]})</p>
            <button onclick="placeBet(${match.id}, '${match.team1}')">Parier ${match.team1}</button>
            <button onclick="placeBet(${match.id}, '${match.team2}')">Parier ${match.team2}</button>
        `;
        matchesDiv.appendChild(matchCard);
    });
}

function placeBet(matchId, team) {
    const match = matches.find(m => m.id === matchId);
    bets.push({ matchId, team, odds: match.odds[team] });
    renderBets();
}

function renderBets() {
    betsDiv.innerHTML = "";
    bets.forEach((bet, index) => {
        const match = matches.find(m => m.id === bet.matchId);
        const betCard = document.createElement("div");
        betCard.className = "bet-card";
        betCard.innerHTML = `
            <p>Match: ${match.team1} vs ${match.team2}</p>
            <p>Votre pari: <strong>${bet.team}</strong> (Cote: ${bet.odds})</p>
        `;
        betsDiv.appendChild(betCard);
    });
}

simulateBtn.addEventListener("click", () => {
    bets.forEach(bet => {
        const match = matches.find(m => m.id === bet.matchId);
        const winner = Math.random() > 0.5 ? match.team1 : match.team2;
        if (winner === bet.team) {
            stats.wins++;
        } else {
            stats.losses++;
        }
    });
    updateChart();
});

let ctx = document.getElementById("statsChart").getContext("2d");
let statsChart = new Chart(ctx, {
    type: "doughnut",
    data: {
        labels: ["Gagnés", "Perdus"],
        datasets: [{
            data: [stats.wins, stats.losses],
            backgroundColor: ["#28a745", "#dc3545"]
        }]
    }
});

function updateChart() {
    statsChart.data.datasets[0].data = [stats.wins, stats.losses];
    statsChart.update();
}

renderMatches();