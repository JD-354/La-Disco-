html><head><base href="about:none">

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title> Fiesta de Colores Mejorada </title>



<!-- Add Bootstrap CSS -->

     <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">



<!-- Keep the confetti and Tone.js scripts -->

    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.5.1/dist/confetti.browser.min.js"></script>

    <script src="https://cdn.jsdelivr.net/npm/tone@14.7.77/build/Tone.min.js"></script>



<!-- Update the custom styles to use Bootstrap classes where possible -->

     <style>

    body {

    min-height: 100vh;

    background: linear-gradient(45deg, #f6d365 0%, #fda085 100%); transition: background 1s ease;

     }



     #color-party-container {

     background-color: rgba(255, 255, 255, 0.9); backdrop-filter: blur(10px);

    border-radius: 1rem;

    }



     #party-title {

     animation: bounce 2s infinite;

     }



      @keyframes bounce {

    0%, 100% { transform: translateY(0); } 50% { transform: translateY(-10px); }

      }



      #color-palette { display: grid;

    grid-template-columns: repeat(auto-fit, minmax(60px, 1fr)); gap: 1rem;

    }



    .color-button { width: 60px; height: 60px;

      border-radius: 50%;

 

     border: 4px solid white; cursor: pointer; transition: all 0.3s ease;

    box-shadow: 0 5px 15px rgba(0,0,0,0.1);

     }



     .color-button:hover {

     transform: scale(1.2) rotate(15deg);

    }



    #new-color-input { width: 60px; height: 60px; border-radius: 50%;

     border: 4px solid white; padding: 0;

    }



     #final-results { display: none; position:   fixed; top: 50%;

    left: 50%;
   
    transform: translate(-50%, -50%);

    z-index: 1000;

    }



     @media (max-width: 576px) {

    .color-button, #new-color-input { width: 50px;
  
     height: 50px;

     }

      }



       @media (max-width: 400px) {

    .color-button, #new-color-input { width: 40px;

    height: 40px;

    }

    }

    </style>

    </head>

     <body class="py-4">

    <div class="container">

    <div id="color-party-container" class="p-4 shadow-lg mx-auto" style="max-width: 800px;">

    <h1 id="party-title" class="text-center mb-4"> Fiesta de Colores </h1>

 
  
    <div id="color-palette" class="my-4"></div>



      <div class="d-flex flex-wrap gap-2 justify-content-center mb-4">

    <input

    type="color" id="new-color-input"  value="#ff6b6b" class="me-2"

    >

    <button onclick="addNewColor()" class="btn btn-primary"> Agregar Color

     </button>

     <button onclick="resetParty()" class="btn btn-info text-white"> Reiniciar Fiesta

     </button>

     <button id="end-party" onclick="endParty()" class="btn btn-danger"> Finalizar Fiesta

    </button>

     </div>



     <div id="vote-display" class="alert alert-info mt-3"></div>

    <div id="countdown" class="text-center text-muted"></div>

    </div>



    <div id="final-results" class="card p-4 text-   center"></div>

     </div>



<!-- Add Bootstrap JS at the end of body -->

     <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>

     <script>

     const initialColors = [

     '#ff6b6b', '#4ecdc4', '#45b7d1',   '#f9d5e5', '#eeac99', '#ff7f50'

    ];



     const soundEffects = {
   
    piano: 'https://assets.mixkit.co/active_storage/sfx/133/133-preview.mp3', drums: 'https://assets.mixkit.co/active_storage/sfx/146/146-preview.mp3', violin: 'https://assets.mixkit.co/active_storage/sfx/203/203-preview.mp3',

     saxophone: 'https://assets.mixkit.co/active_storage/sfx/2425/2425-preview.mp3', trumpet: 'https://assets.mixkit.co/active_storage/sfx/2408/2408-preview.mp3', marimba: 'https://assets.mixkit.co/active_storage/sfx/147/147-preview.mp3', guitar: 'https://assets.mixkit.co/active_storage/sfx/123/123-preview.mp3',

    flute: 'https://assets.mixkit.co/active_storage/sfx/2405/2405-preview.mp3', xylophone: 'https://assets.mixkit.co/active_storage/sfx/135/135-preview.mp3',

 

    harp: 'https://assets.mixkit.co/active_storage/sfx/134/134-preview.mp3', bells: 'https://assets.mixkit.co/active_storage/sfx/136/136-preview.mp3', bongos: 'https://assets.mixkit.co/active_storage/sfx/149/149-preview.mp3'

    };



     let colorVotes = {}; let inactivityTimer;

     let isPartyActive = true; let synth;

    let currentMusicLoop; let currentAudio = null;



     function generateRandomColor() {

     const letters = '0123456789ABCDEF'; let color = '#';

     for (let i = 0; i < 6; i++) {

     color += letters[Math.floor(Math.random() * 16)];

    }

     return color;

     }



     async function initAudio() { await Tone.start();



      synth = new Tone.PolySynth(Tone.Synth, { oscillator: {

type: "sawtooth" // More electronic sound

},

envelope: { attack: 0.01,

decay: 0.3,

sustain: 0.7,

release: 0.2

}

}).toDestination();



const reverb = new Tone.Reverb({ decay: 3,

wet: 0.4

}).toDestination();



const delay = new Tone.PingPongDelay({ delayTime: "16n",

feedback: 0.4,

wet: 0.3

}).toDestination();

 

synth.connect(reverb); synth.connect(delay);

}



document.body.addEventListener('click', function() { if (!synth) initAudio();

}, { once: true });



function playColorSound(color) {

// Stop any currently playing audio if (currentAudio) {

currentAudio.pause(); currentAudio.currentTime = 0;

}



// Get all available sounds

const sounds = Object.values(soundEffects);



// Create a deterministic mapping from color to sound based on the color's hex value

const colorSum = color.slice(1).split('').reduce((sum, char) => sum + char.charCodeAt(0), 0); const soundIndex = colorSum % sounds.length;



// Create and play new audio

const audio = new Audio(sounds[soundIndex]); audio.loop = true;

audio.play();



return audio;

}



function initColorParty() {

const palette = document.getElementById('color-palette'); palette.innerHTML = '';

isPartyActive = true;



initialColors.forEach(color => { createColorButton(color); colorVotes[color] = 0;

});



document.getElementById('final-results').style.display = 'none'; document.getElementById('color-party-container').style.display = 'block'; updateVoteDisplay();

}

 

function createColorButton(color) {

const button = document.createElement('div'); button.classList.add('color-button'); button.style.backgroundColor = color; button.onclick = () => selectColor(color);



document.getElementById('color-palette').appendChild(button);

}



function selectColor(color) { if (!isPartyActive) return;



const title = document.getElementById('party-title'); title.style.color = color;

document.body.style.background = `linear-gradient(45deg, ${color}, ${lightenColor(color)})`;



// Play the sound effect

currentAudio = playColorSound(color);



confetti({ particleCount: 100,

spread: 70, origin: { y: 0.6 }

});



colorVotes[color] = (colorVotes[color] || 0) + 1; updateVoteDisplay();



resetInactivityTimer();

}



function lightenColor(color) {

const r = parseInt(color.substr(1,2), 16); const g = parseInt(color.substr(3,2), 16); const b = parseInt(color.substr(5,2), 16);



return `rgb(${Math.min(r + 50, 255)}, ${Math.min(g + 50, 255)}, ${Math.min(b + 50, 255)})`;

}



function addNewColor() { if (!isPartyActive) return;

const newColor = generateRandomColor(); document.getElementById('new-color-input').value = newColor; createColorButton(newColor);

colorVotes[newColor] = 0;

 

confetti({ particleCount: 50,

spread: 45, origin: { y: 0.7 }

});

}



function updateVoteDisplay() {

const voteDisplay = document.getElementById('vote-display'); const mostPopularColor = Object.keys(colorVotes).reduce(

(a, b) => colorVotes[a] > colorVotes[b] ? a : b

);



voteDisplay.innerHTML = `Color Más Popular:

<span style="color:${mostPopularColor}; font-weight: bold">

${mostPopularColor} (${colorVotes[mostPopularColor]} votos)

</span>`;

}



function resetParty() { if (currentAudio) {

currentAudio.pause(); currentAudio.currentTime = 0; currentAudio = null;

}

document.getElementById('party-title').style.color = 'black'; document.body.style.background = 'linear-gradient(45deg, #f6d365 0%, #fda085 100%)'; initColorParty();



confetti({ particleCount: 200,

spread: 180, origin: { y: 0.5 }

});

}



function endParty() { if (currentAudio) {

currentAudio.pause(); currentAudio.currentTime = 0; currentAudio = null;

}

isPartyActive = false; clearInterval(inactivityTimer);

document.getElementById('countdown').textContent = '';

 

const mostPopularColor = Object.entries(colorVotes).reduce( (max, [color, votes]) => votes > max[1] ? [color, votes] : max, ['', 0]

);



const finalResults = document.getElementById('final-results'); finalResults.style.display = 'block'; finalResults.style.backgroundColor = mostPopularColor[0]; finalResults.innerHTML = `

<h2 style="color: white; text-shadow: 2px 2px 4px rgba(0,0,0,0.3)">

¡Fiesta Finalizada!

</h2>

<p style="color: white; font-size: 1.2rem">

Color más votado: ${mostPopularColor[0]}<br> Total de votos: ${mostPopularColor[1]}

</p>

<button onclick="resetParty()" style="margin-top: 20px" class="btn btn-light"> Iniciar Nueva Fiesta

</button>

`;



confetti({ particleCount: 300,

spread: 180, origin: { y: 0.6 }

});

}



function resetInactivityTimer() { clearTimeout(inactivityTimer);



const countdownElement = document.getElementById('countdown'); let timeLeft = 10; // Changed from 15 to 10

countdownElement.textContent = `Siguiente turno en: ${timeLeft} segundos`; inactivityTimer = setInterval(() => {

timeLeft--;

countdownElement.textContent = `Siguiente turno en: ${timeLeft} segundos`;



if (timeLeft <= 0) { clearInterval(inactivityTimer); if (currentAudio) {

currentAudio.pause(); currentAudio.currentTime = 0; currentAudio = null;

 

}

countdownElement.textContent = '¡Es tu turno! Elige un color ';

}

}, 1000);

}



initColorParty();

</script>

</body>

</html>

