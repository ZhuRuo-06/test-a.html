# test-a.html

<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sistem Kombinasi Karakter</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #f4f4f4;
      margin: 0;
    }
    .container {
      text-align: center;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    button:hover {
      background-color: #45a049;
    }
    #result {
      margin-top: 20px;
      font-size: 24px;
      font-weight: bold;
    }
    #levelButtons {
      display: none;
    }
    #gachaSection {
      display: none;
    }
    .star {
      font-size: 24px;
      color: lightgrey;
      cursor: pointer;
    }
    .star.checked {
      color: black;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Kombinasi Karakter</h1>
    <div id="startSection">
      <button id="startButton">Mulai</button>
    </div>
    <div id="levelButtons">
      <button id="gachaLv1">Gacha Lv 1</button>
      <button id="gachaLv2">Gacha Lv 2</button>
      <button id="gachaLv3">Gacha Lv 3</button>
    </div>
    <div id="gachaSection">
      <button id="spinButton">Spin!</button>
      <button id="backButton">Kembali</button>
      <div id="result"></div>
      <div id="stars"></div>
    </div>
  </div>
  <script>
    // Daftar trait berdasarkan ras
    const traitsByRace = {
      Human: ['None', 'Adaptability', 'Versatile', 'Resourceful', 'Diplomatic', 'Inventive', 'Determined', 'Charismatic', 'Skilled Tradesman', 'Tactical Mind', 'Resilient'],
      Dwarf: ['None', 'Stonefist', 'Deep Delver', 'Mountain\'s Endurance', 'Forge Master', 'Stout Heart', 'Rune Carver', 'Battle Hardy', 'Earthshaker', 'Iron Will', 'Brewmaster'],
      Elf: ['None', 'Keen Senses', 'Elven Grace', 'Nature\'s Friend', 'Arcane Affinity', 'Swift Movement', 'Moonlit Vision', 'Tranquil Soul', 'Forest Guardian', 'Starlit Path', 'Agile Hunter'],
      Aasimar: ['None', 'Celestial Radiance', 'Heavenly Aura', 'Divine Insight', 'Holy Resilience', 'Angelic Guidance', 'Luminous Presence', 'Radiant Shield', 'Seraphic Strength', 'Ethereal Grace', 'Celestial Protection'],
      Demon: ['None', 'Infernal Might', 'Shadow Step', 'Hellish Resilience', 'Fiendish Magic', 'Demonic Influence', 'Abyssal Fortitude', 'Nightmare Visions', 'Dark Pact', 'Unholy Endurance', 'Sinister Presence'],
      Vampir: ['None', 'Life Drain', 'Night Vision', 'Bloodthirst', 'Regeneration', 'Shadow Manipulation', 'Eternal Youth', 'Vampiric Resilience', 'Hypnotic Gaze', 'Nocturnal Advantage', 'Bat Form'],
      Kitsune: ['None', 'Illusion Craft', 'Shape-shift', 'Mystic Charm', 'Foxfire', 'Camouflage', 'Feline Agility', 'Deceptive Strike', 'Enigmatic Presence', 'Swift Reflexes', 'Fox’s Grace'],
      Werewolf: ['None', 'Lunar Empowerment', 'Regeneration', 'Enhanced Senses', 'Feral Strength', 'Berserker Fury', 'Lupine Agility', 'Terrifying Howl', 'Increased Endurance', 'Pack Bond', 'Claw Attack'],
      Dragonborn: ['None', 'Draconic Breath', 'Dragon Scales', 'Flight', 'Fiery Resilience', 'Dragonslayer\'s Strength', 'Elemental Affinity', 'Intimidating Presence', 'Fire Immunity', 'Dragonscale Armor', 'Ancient Wisdom'],
      Tabaxi: ['None', 'Feline Agility', 'Claw Attack', 'Quick Reflexes', 'Stealthy Movements', 'Cat\'s Grace', 'Curious Nature', 'Night Prowler', 'Keen Senses', 'Hunting Instincts', 'Swift Movement']
    };

    // Tingkat kelangkaan ras
    const raceRarities = {
      Human: 90,
      Dwarf: 2,
      Elf: 2,
      Aasimar: 0.5,
      Demon: 0.5,
      Vampir: 0.4,
      Kitsune: 0.6,
      Werewolf: 0.5,
      Dragonborn: 0.1,
      Tabaxi: 0.7
    };

    // Tingkat kelangkaan elemen
    const elementRarities = {
      Neutral: 80,
      Nature: 5.1,
      Earth: 4.5,
      Fire: 3.25,
      Wind: 2.125,
      Water: 1.5625,
      Lightning: 0.078125,
      Wood: 0.0390625,
      Iron: 0.01953125,
      Poison: 0.009765625,
      Light: 0.0048828125,
      Holy: 0.00244140625,
      Demonic: 0.001220703125,
      Death: 0.006103515625,
      Darkness: 0.0030517578125,
      Gravity: 0.000152587890625,
      Time: 0.000762939453125,
      Space: 0.0003814697265625
    };

    // Daftar talent baru
    const talents = {
      'Mana Sensitivity': 3,
      'Mind Resonance': 2,
      'Shadow Walk': 0.1,
      'Chrono Shift': 1,
      'Elemental Fusion': 2,
      'Arcane Sight': 1,
      'Fury Infusion': 3,
      'Echo Manipulation': 2,
      'Dream Weaver': 0.5,
      'Spectral Bond': 1,
      'Gravity Shift': 1,
      'Flame Crafting': 2,
      'Telekinetic Pulse': 1,
      'Aura Enhancement': 3,
      'Ethereal Manipulation': 0.5,
      'Illusionist’s Veil': 2,
      'Spirit Link': 1,
      'Tempest Control': 0.5,
      'Vitality Surge': 3,
      'Pyroclasmic Burst': 0.5,
      'Celestial Guidance': 1,
      'Mystic Healing': 2,
      'Quantum Blink': 0.1,
      'Warding Field': 1,
      'Shadow Bind': 0.5,
      'Psychic Echo': 2,
      'Stormcaller’s Command': 0.1,
      'Astral Projection': 0.1,
      'Luminal Vision': 0.5,
      'Elemental Conjuration': 2
    };

    // Daftar job baru
    const jobs = {
      Warrior: 1,
      Farmer: 2,
      Mage: 0.1,
      Ranger: 1,
      Blacksmith: 2,
      Healer: 1,
      Thief: 1,
      Alchemist: 0.5,
      Sorcerer: 0.5,
      Knight: 1,
      Bard: 2,
      Assassin: 0.5,
      Priest: 1,
      Druid: 0.5,
      Wizard: 0.1
    };

    function generateCombinations() {
      const races = Object.keys(traitsByRace);

      // Pilih ras secara acak dengan mempertimbangkan kelangkaan
      const weightedRaces = [];
      for (const [race, rarity] of Object.entries(raceRarities)) {
        for (let i = 0; i < rarity; i++) {
          weightedRaces.push(race);
        }
      }
      const randomRace = weightedRaces[Math.floor(Math.random() * weightedRaces.length)];
      const traits = traitsByRace[randomRace];

      // Buat array dengan mempertimbangkan tingkat kelangkaan elemen
      const weightedElements = [];
      for (const [element, rarity] of Object.entries(elementRarities)) {
        for (let i = 0; i < rarity; i++) {
          weightedElements.push(element);
        }
      }

      // Buat array untuk traits dengan mempertimbangkan kelangkaan
      const weightedTraits = [];
      for (let i = 0; i < traits.length; i++) {
        if (traits[i] === 'None') {
          for (let j = 0; j < 99; j++) {
            weightedTraits.push(traits[i]);
          }
        } else {
          weightedTraits.push(traits[i]);
        }
      }

      // Menentukan talent dan job secara acak
      const weightedTalents = [];
      for (const [talent, rarity] of Object.entries(talents)) {
        for (let i = 0; i < rarity; i++) {
          weightedTalents.push(talent);
        }
      }

      const weightedJobs = [];
      for (const [job, rarity] of Object.entries(jobs)) {
        for (let i = 0; i < rarity; i++) {
          weightedJobs.push(job);
        }
      }

      const randomElement = weightedElements[Math.floor(Math.random() * weightedElements.length)];
      const randomTalent = weightedTalents[Math.floor(Math.random() * weightedTalents.length)];
      const randomJob = weightedJobs[Math.floor(Math.random() * weightedJobs.length)];
      const randomTrait = weightedTraits[Math.floor(Math.random() * weightedTraits.length)];

      return {
        race: randomRace,
        talent: randomTalent,
        job: randomJob,
        trait: randomTrait,
        element: randomElement
      };
    }

    // Fungsi untuk menghitung rarity rata-rata dari ras, elemen, trait, talent, dan job
    function calculateAvarageRarity(raceRarity, elementRarity, traitRarity, talentRarity, jobRarity) {
      return (raceRarity + elementRarity + traitRarity + talentRarity + jobRarity) / 5;
    }

    // Fungsi untuk menilai karakter berdasarkan bintang
    function getStarRating(rarity) {
      if (rarity <= 0.1) return 5;
      if (rarity <= 2) return 4;
      if (rarity <= 9) return 3;
      if (rarity <= 15) return 2;
      return 1;
    }

    function updateStars(rating) {
      const starsContainer = document.getElementById('stars');
      starsContainer.innerHTML = '';
      for (let i = 0; i < 5; i++) {
        const star = document.createElement('span');
        star.className = 'star';
        star.innerHTML = '★';
        if (i < rating) {
          star.classList.add('checked');
        }
        starsContainer.appendChild(star);
      }
    }

    document.getElementById('spinButton').addEventListener('click', function() {
      const combination = generateCombinations();
      const raceRarity = raceRarities[combination.race];
      const elementRarity = elementRarities[combination.element];
      const traitRarity = 1; // Default value for trait rarity
      const talentRarity = talents[combination.talent];
      const jobRarity = jobs[combination.job];
      const combinedRarity = calculateAvarageRarity(raceRarity, elementRarity, traitRarity, talentRarity, jobRarity);
      const starRating = getStarRating(combinedRarity);
      document.getElementById('result').innerHTML = `
        <p>Ras: ${combination.race}</p>
        <p>Talent: ${combination.talent}</p>
        <p>Job: ${combination.job}</p>
        <p>Trait: ${combination.trait}</p>
        <p>Element: ${combination.element}</p>
      `;
      updateStars(starRating);
    });

    document.getElementById('startButton').addEventListener('click', function() {
      document.getElementById('startSection').style.display = 'none';
      document.getElementById('levelButtons').style.display = 'block';
    });

    document.getElementById('gachaLv1').addEventListener('click', function() {
      document.getElementById('levelButtons').style.display = 'none';
      document.getElementById('gachaSection').style.display = 'block';
    });

    document.getElementById('gachaLv2').addEventListener('click', function() {
      document.getElementById('levelButtons').style.display = 'none';
      document.getElementById('gachaSection').style.display = 'block';
    });

    document.getElementById('gachaLv3').addEventListener('click', function() {
      document.getElementById('levelButtons').style.display = 'none';
      document.getElementById('gachaSection').style.display = 'block';
    });

    document.getElementById('backButton').addEventListener('click', function() {
      document.getElementById('gachaSection').style.display = 'none';
      document.getElementById('levelButtons').style.display = 'block';
    });
  </script>
</body>
</html>