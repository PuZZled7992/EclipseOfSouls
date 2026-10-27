const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const menuScreen = document.getElementById('menu-screen');
const creationScreen = document.getElementById('creation-screen');
const gameScreen = document.getElementById('game-screen');
const endScreen = document.getElementById('end-screen');

const startBtn = document.getElementById('start-btn');
const launchGameBtn = document.getElementById('launch-game-btn');
const restartBtn = document.getElementById('restart-btn');
const raceOptionsContainer = document.getElementById('race-options');
const statSummary = document.getElementById('stat-summary');
const classPreview = document.getElementById('class-preview');
const playerNameInput = document.getElementById('player-name');

const hudName = document.getElementById('hud-name');
const hudRace = document.getElementById('hud-race');
const hudClass = document.getElementById('hud-class');
const hudRegion = document.getElementById('hud-region');
const hpBar = document.getElementById('hp-bar');
const mpBar = document.getElementById('mp-bar');
const hpText = document.getElementById('hp-text');
const mpText = document.getElementById('mp-text');
const floatingMessage = document.getElementById('floating-message');
const storyPanel = document.getElementById('story-panel');
const battleOverlay = document.getElementById('battle-overlay');
const battleTitle = document.getElementById('battle-title');
const battleBossName = document.getElementById('battle-boss-name');
const bossBar = document.getElementById('boss-bar');
const bossText = document.getElementById('boss-text');
const battleLog = document.getElementById('battle-log');
const endingTitle = document.getElementById('ending-title');
const endingText = document.getElementById('ending-text');

const attackBtn = document.getElementById('attack-btn');
const dodgeBtn = document.getElementById('dodge-btn');
const abilityBtn = document.getElementById('ability-btn');

const world = {
  width: 2000,
  height: 1600,
  regions: [
    {
      id: 'capital',
      name: 'Shattered Capital',
      x: 260,
      y: 310,
      radius: 82,
      color: '#d0a86e',
      bossName: 'The Hollow in the Thorns',
      bossHp: 150,
      secretWeapon: 'The Mourning Saber',
      lore: 'The capital is the first broken echo of the old throne. The people still pray beneath the ruins.',
      visited: false,
      bossDefeated: false,
      secretCollected: false,
      storyline: 'The first wound in the sky opens inside the capital. The people call it the Hollow in the Thorns.'
    },
    {
      id: 'wilds',
      name: 'Ashen Wilds',
      x: 530,
      y: 520,
      radius: 92,
      color: '#ad8b62',
      bossName: 'Lady Veyra, the Ember Widow',
      bossHp: 190,
      secretWeapon: 'The Moon-Split Spear',
      lore: 'The wilds burn forever, but the ash still remembers every funeral chant.',
      visited: false,
      bossDefeated: false,
      secretCollected: false,
      storyline: 'Veyra weaves fire through old graves. To pass, you must step through the lower mausoleum.'
    },
    {
      id: 'forest',
      name: 'Twilight Forest',
      x: 840,
      y: 390,
      radius: 90,
      color: '#6da283',
      bossName: 'Gorrath of the Broken Sky',
      bossHp: 220,
      secretWeapon: 'The Hollow Crown',
      lore: 'The trees whisper names of lost kings, and every branch points to a forgotten sky.',
      visited: false,
      bossDefeated: false,
      secretCollected: false,
      storyline: 'Gorrath crashes through the canopy as if the world itself were a broken bell.'
    },
    {
      id: 'coast',
      name: 'Bone Coast',
      x: 1180,
      y: 690,
      radius: 90,
      color: '#7aa7cc',
      bossName: 'The King of Dust',
      bossHp: 250,
      secretWeapon: 'The Sunless Staff',
      lore: 'The sea is dry, the bones are loud, and the dead still march with the tide.',
      visited: false,
      bossDefeated: false,
      secretCollected: false,
      storyline: 'Every tide returns carrying the crown of a drowned tyrant.'
    },
    {
      id: 'mountain',
      name: 'Stormbroken Mountain',
      x: 1550,
      y: 400,
      radius: 98,
      color: '#a7b7d9',
      bossName: 'The Second Moon',
      bossHp: 280,
      secretWeapon: 'The Eclipse Anchor',
      lore: 'The mountain carries a moon that was never meant to remain in the sky.',
      visited: false,
      bossDefeated: false,
      secretCollected: false,
      storyline: 'The old god’s shadow is trapped in the peak. If it wakes, the eclipse will devour the world.'
    },
    {
      id: 'sanctum',
      name: 'Drowned Sanctum',
      x: 1700,
      y: 1010,
      radius: 110,
      color: '#8e78ff',
      bossName: 'The Forgotten God',
      bossHp: 320,
      secretWeapon: 'The Last God’s Halo',
      lore: 'At the heart of the shattered world lies the final god, buried beneath dead light.',
      visited: false,
      bossDefeated: false,
      secretCollected: false,
      storyline: 'This is the final gate. Beyond it, the world will be reborn or erased.'
    }
  ],
  paths: [
    [0, 1], [1, 2], [2, 3], [3, 4], [4, 5]
  ]
};

const races = [
  {
    name: 'Human',
    bonus: { strength: 2, agility: 2, intelligence: 2, vitality: 2, defense: 2, luck: 2, speed: 2, magic: 2 },
    desc: 'Balanced and adaptable.'
  },
  {
    name: 'Elf',
    bonus: { strength: 0, agility: 5, intelligence: 4, vitality: 1, defense: 1, luck: 2, speed: 4, magic: 3 },
    desc: 'Swift and magically attuned.'
  },
  {
    name: 'Dwarf',
    bonus: { strength: 5, agility: 1, intelligence: 1, vitality: 4, defense: 4, luck: 1, speed: 1, magic: 0 },
    desc: 'Stone-strong and highly resistant.'
  },
  {
    name: 'Orc',
    bonus: { strength: 6, agility: 2, intelligence: 0, vitality: 3, defense: 3, luck: 1, speed: 2, magic: 1 },
    desc: 'Unmatched brute force.'
  },
  {
    name: 'Voidkin',
    bonus: { strength: 1, agility: 2, intelligence: 5, vitality: 1, defense: 1, luck: 4, speed: 3, magic: 5 },
    desc: 'Arcane and unpredictable.'
  },
  {
    name: 'Celestialborn',
    bonus: { strength: 2, agility: 4, intelligence: 4, vitality: 2, defense: 2, luck: 2, speed: 3, magic: 3 },
    desc: 'Touched by the heavenly fracture.'
  },
  {
    name: 'Beastkin',
    bonus: { strength: 2, agility: 5, intelligence: 1, vitality: 2, defense: 2, luck: 3, speed: 5, magic: 1 },
    desc: 'Fast, fierce, and tactical.'
  }
];

const initialStats = {
  strength: 10,
  agility: 10,
  intelligence: 10,
  vitality: 10,
  defense: 10,
  luck: 10,
  speed: 10,
  magic: 10
};

const game = {
  state: 'menu',
  selectedRace: races[0],
  chosenName: 'Aeris',
  className: 'Warden',
  isGodClass: false,
  player: {
    x: 160,
    y: 250,
    radius: 14,
    speed: 220,
    hp: 100,
    maxHp: 100,
    mp: 40,
    maxMp: 40,
    attack: 18,
    dodge: 12,
    crit: 0.08,
    lvl: 1,
    xp: 0,
    weapon: 'Null Blade',
    stats: { ...initialStats },
    inventory: []
  },
  battle: null,
  nearbyRegion: null,
  story: 'A calm wind touches the broken kingdom. Your destiny is waiting.',
  lastMessage: '',
  currentQuest: 'Reach the first ruined gate and defeat the first beast.'
};

const keys = {};

function showMessage(msg) {
  game.lastMessage = msg;
  floatingMessage.textContent = msg;
  floatingMessage.classList.add('visible');
  clearTimeout(showMessage.timeout);
  showMessage.timeout = setTimeout(() => floatingMessage.classList.remove('visible'), 1800);
}

function setScreen(screenName) {
  menuScreen.classList.add('hidden');
  creationScreen.classList.add('hidden');
  gameScreen.classList.add('hidden');
  endScreen.classList.add('hidden');

  if (screenName === 'menu') menuScreen.classList.remove('hidden');
  if (screenName === 'creation') creationScreen.classList.remove('hidden');
  if (screenName === 'game') gameScreen.classList.remove('hidden');
  if (screenName === 'end') endScreen.classList.remove('hidden');
}

function buildRaceButtons() {
  raceOptionsContainer.innerHTML = '';

  for (const race of races) {
    const btn = document.createElement('button');
    btn.className = 'race-button selected';
    if (race.name === game.selectedRace.name) btn.classList.add('selected');
    btn.type = 'button';
    btn.innerHTML = `<strong>${race.name}</strong><small>${race.desc}</small>`;
    btn.addEventListener('click', () => {
      game.selectedRace = race;
      buildRaceButtons();
      updateCharacterPreview();
    });
    raceOptionsContainer.appendChild(btn);
  }
}

function updateCharacterPreview() {
  const chosenName = playerNameInput.value.trim() || 'Aeris';
  const race = game.selectedRace;
  const stats = race.bonus;
  const statsDisplay = [];

  for (const [key, value] of Object.entries(stats)) {
    const label = key.charAt(0).toUpperCase() + key.slice(1);
    statsDisplay.push(`<div><strong>${label}</strong>: +${value}</div>`);
  }

  classPreview.textContent = chosenName ? `Warden of the ${race.name}` : 'Warden';
  statSummary.innerHTML = statsDisplay.join('');

  if (Math.random() < 0.05) {
    game.isGodClass = true;
    game.className = 'GOD';
    classPreview.textContent = 'GOD CLASS — First Dawn Bearer';
    classPreview.style.color = '#ffd86a';
  } else {
    game.isGodClass = false;
    game.className = 'Warden';
    classPreview.style.color = '#7be7ff';
  }
}

function applyRaceStats() {
  const race = game.selectedRace;
  const base = { ...initialStats };

  for (const stat of Object.keys(base)) {
    base[stat] += race.bonus[stat] || 0;
  }

  game.player.stats = base;
  game.player.maxHp = 100 + base.vitality * 4;
  game.player.hp = game.player.maxHp;
  game.player.maxMp = 40 + base.magic * 3;
  game.player.mp = game.player.maxMp;
  game.player.attack = 18 + base.strength + base.magic / 2;
  game.player.dodge = 12 + base.agility / 2;
  game.player.crit = 0.08 + base.luck * 0.004;
  game.player.speed = 220 + base.speed * 2.5;

  if (game.isGodClass) {
    game.player.maxHp += 100;
    game.player.hp = game.player.maxHp;
    game.player.maxMp += 60;
    game.player.mp = game.player.maxMp;
    game.player.attack += 50;
    game.player.crit += 0.15;
    game.player.weapon = 'The Last God’s Halo';
    showMessage('A divine pulse awakens in your blood. The GOD class has appeared.');
  }
}

function startGame() {
  const inputName = playerNameInput.value.trim();
  game.chosenName = inputName || 'Aeris';
  game.selectedRace = game.selectedRace || races[0];

  const godRoll = Math.random();
  game.isGodClass = godRoll < 0.05;
  game.className = game.isGodClass ? 'GOD' : 'Warden';

  if (game.isGodClass) {
    game.currentQuest = 'The First Dawn has marked you. Seek the Drowned Sanctum and break the eclipse.';
  }

  applyRaceStats();
  game.player.inventory = [];
  for (const region of world.regions) {
    region.visited = false;
    region.bossDefeated = false;
    region.secretCollected = false;
  }

  game.state = 'game';
  game.player.x = 160;
  game.player.y = 250;
  game.nearbyRegion = null;
  setScreen('game');
  updateHud();
  showMessage('The eclipse has broken. The world awaits your choice.');
}

function updateHud() {
  hudName.textContent = game.chosenName;
  hudRace.textContent = game.selectedRace.name;
  hudClass.textContent = game.className;

  const regionName = game.nearbyRegion ? game.nearbyRegion.name : 'Travelling the Wilds';
  hudRegion.textContent = regionName;

  hpBar.style.width = `${(game.player.hp / game.player.maxHp) * 100}%`;
  mpBar.style.width = `${(game.player.mp / game.player.maxMp) * 100}%`;
  hpText.textContent = `${Math.floor(game.player.hp)}/${game.player.maxHp}`;
  mpText.textContent = `${Math.floor(game.player.mp)}/${game.player.maxMp}`;
}

function getDistance(a, b) {
  return Math.hypot(a.x - b.x, a.y - b.y);
}

function findNearbyRegion() {
  let nearest = null;
  let nearestDistance = Infinity;

  for (const region of world.regions) {
    const dist = getDistance({ x: game.player.x, y: game.player.y }, { x: region.x, y: region.y });
    if (dist < region.radius + 30 && dist < nearestDistance) {
      nearest = region;
      nearestDistance = dist;
    }
  }

  game.nearbyRegion = nearest;
}

function setStoryPanel(title, text) {
  storyPanel.innerHTML = `<h4>${title}</h4><p>${text}</p>`;
  storyPanel.classList.remove('hidden');
}

function hideStoryPanel() {
  storyPanel.classList.add('hidden');
}

function beginBattle(region) {
  const enemy = {
    name: region.bossName,
    hp: region.bossHp,
    maxHp: region.bossHp,
    damage: Math.floor(region.bossHp / 8) + 12,
    crit: 0.1,
    calcDamage: () => {
      const base = enemy.damage + (Math.random() * 12);
      return Math.max(6, Math.round(base));
    }
  };

  game.battle = { region, enemy, turn: 1, playerAttackCooldown: 0 };
  battleTitle.textContent = `${region.name} | Boss Encounter`;
  battleBossName.textContent = enemy.name;
  battleLog.textContent = `${region.storyline}\n${enemy.name} descends from the eclipse.`;
  bossBar.style.width = '100%';
  bossText.textContent = `${enemy.hp}/${enemy.maxHp}`;
  battleOverlay.classList.remove('hidden');
}

function resolvePlayerAttack() {
  if (!game.battle) return;

  const battle = game.battle;
  const player = game.player;
  const damage = Math.round(player.attack + Math.random() * 18);
  const critChance = Math.random();
  const crit = critChance < player.crit;
  const final = crit ? damage + 20 : damage;

  battle.enemy.hp -= final;
  battleLog.textContent = crit
    ? `Critical hit! ${battle.enemy.name} takes ${final} damage.`
    : `${battle.enemy.name} takes ${final} damage.`;

  updateBossBar();

  if (battle.enemy.hp <= 0) {
    battle.enemy.hp = 0;
    finishBattle();
    return;
  }

  const enemyDamage = battle.enemy.calcDamage();
  const dodgeRoll = Math.random() * 100;
  if (dodgeRoll < player.dodge) {
    battleLog.textContent += ` You sidestep the blow and avoid ${enemyDamage} damage.`;
  } else {
    const reduced = Math.max(4, enemyDamage - (player.stats.defense * 0.35));
    game.player.hp = Math.max(0, game.player.hp - reduced);
    battleLog.textContent += ` ${battle.enemy.name} deals ${reduced} damage.`;
    if (game.player.hp <= 0) {
      endGame('The Eclipse devours the Warden. You fall before the light returns.', 'Defeat');
    }
  }

  updateHud();
}

function resolveDodge() {
  if (!game.battle) return;
  const dodgeBoost = Math.random() * 100 < game.player.dodge + 10;
  if (dodgeBoost) {
    battleLog.textContent = 'You dodge the incoming blow and recover your stance.';
    game.player.mp = Math.min(game.player.maxMp, game.player.mp + 4);
  } else {
    const enemyDamage = game.battle.enemy.calcDamage() - 4;
    game.player.hp = Math.max(0, game.player.hp - enemyDamage);
    battleLog.textContent = `Your dodge is imperfect. ${game.battle.enemy.name} lands ${enemyDamage} damage.`;
  }
  updateHud();

  if (game.player.hp <= 0) {
    endGame('The Eclipse devours the Warden. The kingdom sinks back into silence.', 'Defeat');
  }
}

function resolveAbility() {
  if (!game.battle) return;
  if (game.player.mp < 12) {
    battleLog.textContent = 'Your soul is too spent. You need more mana.';
    return;
  }

  game.player.mp -= 12;
  const burst = Math.round(game.player.attack * 1.7 + Math.random() * 26);
  game.battle.enemy.hp -= burst;
  battleLog.textContent = `Soul Burst erupts with ${burst} damage.`;
  updateBossBar();

  if (game.battle.enemy.hp <= 0) {
    finishBattle();
    return;
  }

  const enemyDamage = game.battle.enemy.calcDamage() + 5;
  const reduced = Math.max(6, enemyDamage - game.player.stats.defense * 0.5);
  game.player.hp = Math.max(0, game.player.hp - reduced);
  battleLog.textContent += ` The enemy retaliates for ${reduced} damage.`;
  updateHud();

  if (game.player.hp <= 0) {
    endGame('Your soul is consumed by the eclipse. The world waits for another Warden.', 'Defeat');
  }
}

function updateBossBar() {
  const battle = game.battle;
  if (!battle) return;
  const width = (battle.enemy.hp / battle.enemy.maxHp) * 100;
  bossBar.style.width = `${Math.max(0, width)}%`;
  bossText.textContent = `${Math.max(0, battle.enemy.hp)}/${battle.enemy.maxHp}`;
}

function finishBattle() {
  const current = game.battle.region;
  current.bossDefeated = true;
  current.visited = true;

  const secretTitle = `Relic Uncovered: ${current.secretWeapon}`;
  const secretText = `The battle ends as the sky splits apart. A hidden weapon sings from the ruins: ${current.secretWeapon}.`;
  setStoryPanel(secretTitle, secretText);

  if (!current.secretCollected) {
    game.player.inventory.push(current.secretWeapon);
    current.secretCollected = true;
  }

  showMessage(`${current.secretWeapon} is now yours.`);
  battleOverlay.classList.add('hidden');
  game.battle = null;

  if (isFinalRegionCompleted()) {
    setTimeout(() => endGame('The Forgotten God is slain and the sky opens once more. The world has been reborn under your hand.', 'The Dawn Returns'), 900);
  }
}

function isFinalRegionCompleted() {
  return world.regions.every((r) => r.bossDefeated);
}

function endGame(text, title) {
  game.state = 'end';
  endingTitle.textContent = title;
  endingText.textContent = text;
  battleOverlay.classList.add('hidden');
  setScreen('end');
}

function handleRegionInteraction() {
  if (!game.nearbyRegion || game.battle) return;

  const region = game.nearbyRegion;
  if (!region.visited) {
    region.visited = true;
    setStoryPanel(region.name, region.storyline);
    beginBattle(region);
    return;
  }

  if (region.bossDefeated) {
    setStoryPanel(`${region.name} Cleared`, `${region.lore} The mission advances to the next broken horizon.`);
    showMessage(`You have cleared ${region.name}.`);
    return;
  }

  setStoryPanel(region.name, region.lore);
  beginBattle(region);
}

function updatePlayerMovement() {
  let dx = 0;
  let dy = 0;

  if (keys['ArrowLeft'] || keys['a']) dx -= 1;
  if (keys['ArrowRight'] || keys['d']) dx += 1;
  if (keys['ArrowUp'] || keys['w']) dy -= 1;
  if (keys['ArrowDown'] || keys['s']) dy += 1;

  if (dx !== 0 || dy !== 0) {
    const length = Math.hypot(dx, dy) || 1;
    dx /= length;
    dy /= length;

    game.player.x += dx * game.player.speed * (1 / 60);
    game.player.y += dy * game.player.speed * (1 / 60);
    game.player.x = Math.max(40, Math.min(world.width - 40, game.player.x));
    game.player.y = Math.max(40, Math.min(world.height - 40, game.player.y));
  }

  findNearbyRegion();
  const nearby = game.nearbyRegion;
  if (nearby) {
    hudRegion.textContent = nearby.name;
    if (nearby.bossDefeated) {
      showMessage(`${nearby.name} cleared. The next path awakens.`);
    }
  }
}

function renderWorld() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
  gradient.addColorStop(0, '#1f223e');
  gradient.addColorStop(1, '#0c1121');
  ctx.fillStyle = gradient;
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  const camX = Math.max(0, Math.min(world.width - canvas.width, game.player.x - canvas.width / 2));
  const camY = Math.max(0, Math.min(world.height - canvas.height, game.player.y - canvas.height / 2));

  ctx.save();
  ctx.translate(-camX, -camY);

  for (let i = 0; i < 20; i++) {
    ctx.fillStyle = 'rgba(255,255,255,0.02)';
    ctx.fillRect(40 + i * 90, 40, 2, world.height - 80);
    ctx.fillRect(40, 40 + i * 70, world.width - 80, 2);
  }

  for (const region of world.regions) {
    ctx.beginPath();
    ctx.fillStyle = `${region.color}88`;
    ctx.arc(region.x, region.y, region.radius + 4, 0, Math.PI * 2);
    ctx.fill();

    ctx.beginPath();
    ctx.fillStyle = region.color;
    ctx.arc(region.x, region.y, region.radius, 0, Math.PI * 2);
    ctx.fill();

    ctx.fillStyle = '#f2f5ff';
    ctx.font = '14px Segoe UI';
    ctx.fillText(region.name, region.x - 50, region.y - region.radius - 20);

    if (region.bossDefeated) {
      ctx.fillStyle = '#7ef2a8';
      ctx.fillText('Cleared', region.x - 25, region.y + region.radius + 26);
    }
  }

  ctx.beginPath();
  ctx.fillStyle = '#f7d58f';
  ctx.arc(game.player.x, game.player.y, game.player.radius, 0, Math.PI * 2);
  ctx.fill();

  ctx.restore();
}

function gameLoop() {
  updatePlayerMovement();
  renderWorld();
  updateHud();
  if (game.state === 'game') {
    requestAnimationFrame(gameLoop);
  }
}

function bindControls() {
  window.addEventListener('keydown', (event) => {
    keys[event.key.toLowerCase()] = true;
    if (event.key === 'e' || event.key === 'E') {
      handleRegionInteraction();
    }
    if (event.key === 'j' || event.key === 'J') {
      resolvePlayerAttack();
    }
    if (event.key === 'k' || event.key === 'K') {
      resolveDodge();
    }
    if (event.key === 'l' || event.key === 'L') {
      resolveAbility();
    }
  });

  window.addEventListener('keyup', (event) => {
    keys[event.key.toLowerCase()] = false;
  });

  attackBtn.addEventListener('click', resolvePlayerAttack);
  dodgeBtn.addEventListener('click', resolveDodge);
  abilityBtn.addEventListener('click', resolveAbility);

  startBtn.addEventListener('click', () => {
    setScreen('creation');
    updateCharacterPreview();
  });

  launchGameBtn.addEventListener('click', startGame);
  restartBtn.addEventListener('click', () => {
    setScreen('menu');
    game.state = 'menu';
    playerNameInput.value = 'Aeris';
    game.selectedRace = races[0];
    buildRaceButtons();
    updateCharacterPreview();
  });
}

buildRaceButtons();
updateCharacterPreview();
setScreen('menu');
bindControls();
requestAnimationFrame(gameLoop);
