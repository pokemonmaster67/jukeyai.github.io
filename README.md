<html><head><base href="." />
<title>Jukey - AI Lyrics Generator</title>
<style>
:root {
  --primary: #6a4c93;
  --secondary: #8ac6d1;
  --background: #1a1a1a;
  --text: #ffffff;
}

body {
  margin: 0;
  padding: 20px;
  font-family: 'Segoe UI', system-ui, sans-serif;
  background: var(--background);
  color: var(--text);
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.container {
  max-width: 800px;
  width: 100%;
}

.logo {
  margin-bottom: 2rem;
}

.generator-form {
  background: rgba(255,255,255,0.1);
  padding: 2rem;
  border-radius: 15px;
  margin-bottom: 2rem;
}

input, select, textarea {
  width: 100%;
  padding: 12px;
  margin: 8px 0;
  background: rgba(255,255,255,0.05);
  border: 1px solid var(--secondary);
  border-radius: 8px;
  color: var(--text);
}

select option:disabled {
  color: #666;
  font-style: italic;
}

button {
  background: var(--primary);
  color: white;
  padding: 12px 24px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1.1rem;
  transition: transform 0.2s;
}

button:hover {
  transform: scale(1.05);
}

.output {
  white-space: pre-wrap;
  background: rgba(255,255,255,0.05);
  padding: 2rem;
  border-radius: 15px;
  min-height: 200px;
}

.loading {
  display: none;
  text-align: center;
  margin: 20px 0;
}

.wave {
  display: inline-block;
  animation: wave 1s infinite;
  margin: 0 2px;
}

@keyframes wave {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.human-meter {
  background: rgba(255,255,255,0.1);
  padding: 1rem;
  border-radius: 15px;
  margin-top: 1rem;
}

.meter-container {
  background: rgba(255,255,255,0.05);
  border-radius: 20px;
  height: 20px;
  position: relative;
  overflow: hidden;
}

.meter-fill {
  height: 100%;
  background: linear-gradient(90deg, #ff6b6b, #4ecdc4);
  transition: width 1s ease-in-out;
  position: relative;
}

.meter-label {
  position: absolute;
  width: 100%;
  text-align: center;
  font-size: 0.9rem;
  line-height: 20px;
  color: white;
  text-shadow: 1px 1px 2px rgba(0,0,0,0.5);
  z-index: 1;
}

.analysis {
  margin-top: 1rem;
  font-style: italic;
  color: var(--secondary);
}

/* Add new styles for chopper switch */
.chopper-switch {
  display: none;
  margin-top: 1rem;
}

.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
}

.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(255,255,255,0.1);
  transition: .4s;
  border-radius: 34px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: var(--secondary);
  transition: .4s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: var(--primary);
}

input:checked + .slider:before {
  transform: translateX(26px);
}

.chopper-label {
  margin-left: 1rem;
  vertical-align: super;
}
</style>
</head>
<body>
<div class="container">
  <div class="logo">
    <svg width="200" height="80" viewBox="0 0 200 80">
      <text x="50%" y="50%" font-family="Arial" font-size="40" fill="var(--secondary)" text-anchor="middle" dominant-baseline="middle">
        JUKEY
      </text>
      <circle cx="160" cy="40" r="15" fill="var(--primary)">
        <animate attributeName="r" values="15;18;15" dur="2s" repeatCount="indefinite"/>
      </circle>
    </svg>
  </div>

  <div class="generator-form">
    <h2>Generate AI Lyrics</h2>
    
    <select id="model">
      <option value="jukey1">Jukey 1.0</option>
      <option value="jukey1.5" disabled>Jukey 1.5 (Coming Soon)</option>
    </select>

    <div class="profanity-switch" style="margin: 1rem 0;">
      <label class="switch">
        <input type="checkbox" id="profanityMode">
        <span class="slider"></span>
      </label>
      <span class="chopper-label">Allow Explicit Lyrics</span>
    </div>
    
    <select id="genre" onchange="toggleChopperSwitch()">
      <option value="pop">Pop</option>
      <option value="rock">Rock</option>
      <option value="rap">Rap</option>
      <option value="country">Country</option>
      <option value="edm">EDM</option>
    </select>
    
    <div id="chopperSwitch" class="chopper-switch">
      <label class="switch">
        <input type="checkbox" id="chopperMode">
        <span class="slider"></span>
      </label>
      <span class="chopper-label">Chopper Style</span>
    </div>

    <input type="text" id="topic" placeholder="What's your song about?">
    
    <select id="mood">
      <option value="happy">Happy</option>
      <option value="sad">Sad</option>
      <option value="energetic">Energetic</option>
      <option value="romantic">Romantic</option>
      <option value="angry">Angry</option>
    </select>
    
    <button onclick="generateLyrics()">Generate Lyrics</button>
  </div>

  <div class="loading" id="loading">
    <span class="wave">🎵</span>
    <span class="wave" style="animation-delay: 0.2s">🎵</span>
    <span class="wave" style="animation-delay: 0.4s">🎵</span>
  </div>

  <div class="output" id="output"></div>

  <div class="human-meter">
    <h3>Humanity Analysis</h3>
    <div class="meter-container">
      <div class="meter-label">Analyzing...</div>
      <div class="meter-fill" id="humanityMeter" style="width: 0%"></div>
    </div>
    <div class="analysis" id="analysis"></div>
  </div>
</div>

<script>
async function analyzeLyrics(lyrics) {
  try {
    const response = await fetch('/api/ai_completion', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json',
      },
      body: JSON.stringify({
        prompt: `Analyze these song lyrics and evaluate their human qualities. Consider emotional depth, authenticity, use of personal details, metaphor complexity, and natural language patterns.
        
        <typescript-interface>
        interface Response {
          humanityScore: number;
          analysis: string;
          strengths: string[];
          suggestions: string[];
        }
        </typescript-interface>
        
        <example>
        {
          "humanityScore": 85,
          "analysis": "Strong emotional narrative with vivid personal details",
          "strengths": ["Natural word choice", "Complex metaphors", "Emotional depth"],
          "suggestions": ["Add more sensory details", "Vary sentence structure"]
        }
        </example>`,
        data: lyrics
      }),
    });

    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error analyzing lyrics:', error);
    return null;
  }
}

function toggleChopperSwitch() {
  const genre = document.getElementById('genre').value;
  const chopperSwitch = document.getElementById('chopperSwitch');
  chopperSwitch.style.display = genre === 'rap' ? 'block' : 'none';
}

function convertToChopperStyle(lyrics) {
  return lyrics.split(' ').join('-');
}

async function generateLyrics() {
  const model = document.getElementById('model').value;
  const genre = document.getElementById('genre').value;
  const topic = document.getElementById('topic').value;
  const mood = document.getElementById('mood').value;
  const chopperMode = document.getElementById('chopperMode').checked;
  const allowProfanity = document.getElementById('profanityMode').checked;
  
  if (!topic) {
    alert('Please enter a topic for your song!');
    return;
  }

  const loading = document.getElementById('loading');
  const output = document.getElementById('output');
  const humanityMeter = document.getElementById('humanityMeter');
  const analysis = document.getElementById('analysis');
  
  loading.style.display = 'block';
  output.textContent = '';
  humanityMeter.style.width = '0%';
  analysis.textContent = '';

  try {
    const response = await fetch('/api/ai_completion', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json',
      },
      body: JSON.stringify({
        prompt: `Write deeply personal, raw ${genre} lyrics about ${topic} with a ${mood} mood. ${allowProfanity ? 'Include explicit language and raw emotional expression.' : 'Keep language clean and radio-friendly.'} Include emotional vocal expressions and ad-libs. Focus on:
        - Vulnerable "I" statements expressing deep emotions
        - Natural speech patterns with emotional outbursts (Ooooh!, Yeah!, Whoa!)
        - Soul-bearing confessions and internal struggles
        - Repeating hooks that build emotional intensity
        - Intimate personal details and specific memories
        - Raw, unfiltered feelings ${allowProfanity ? 'with explicit language' : ''}
        - Contrast between strength and vulnerability
        - Voice breaks and emotional emphasis
        ${genre === 'rap' ? '- Fast-paced delivery with complex rhyme schemes' : ''}
        
        <typescript-interface>
        interface Response {
          title: string;
          verses: string[];
          chorus: string;
          bridge?: string;
          outro?: string;
        }
        </typescript-interface>
        
        <example>
        {
          "title": "Shattered Mirrors",
          "verses": [
            "Ohhh, I've been hiding in these shadows\\nTrying to piece myself together, yeah!\\nEvery morning's another battle (another battle!)\\nPretending I'm okay, whoa-oh!\\nBut I'm breaking, I'm breaking inside\\nCan't keep wearing this disguise",
            "Yeahhh, you saw right through the walls I built\\nAll these stories that I tell myself, oh-oh!\\nI'm terrified of being real (so terrified!)\\nBut baby I can't help myself\\nIt's complicated, so complicated\\nI'm suffocating in these feelings"
          ],
          "chorus": "Ooooh, and I just want somebody to see me!\\nYeahhh, I just need somebody to hear me!\\nWhoa-oh, but I'm too scared to let you in\\nToo scared to begin\\nYeah, yeah, yeahhh!",
          "bridge": "Maybe I'm just fooling myself (fooling myself!)\\nMaybe I'm somebody else\\nOh-oh-oh, somebody help me find my way\\nFind my way back home",
          "outro": "I'm lost, I'm found\\nI'm up, I'm down\\nOoooh, somebody save me from myself!\\nYeah-eah-eah!"
        }
        </example>`,
        data: {
          model,
          genre,
          topic,
          mood
        }
      }),
    });

    const data = await response.json();
    
    let formattedLyrics = `🎵 ${data.title} 🎵\n\n`;
    
    data.verses.forEach((verse, index) => {
      let verseText = verse;
      if (genre === 'rap' && chopperMode) {
        verseText = convertToChopperStyle(verse);
      }
      formattedLyrics += `[Verse ${index + 1}]\n${verseText}\n\n`;
    });
    
    let chorusText = data.chorus;
    if (genre === 'rap' && chopperMode) {
      chorusText = convertToChopperStyle(data.chorus);
    }
    formattedLyrics += `[Chorus]\n${chorusText}\n\n`;
    
    if (data.bridge) {
      let bridgeText = data.bridge;
      if (genre === 'rap' && chopperMode) {
        bridgeText = convertToChopperStyle(data.bridge);
      }
      formattedLyrics += `[Bridge]\n${bridgeText}\n\n`;
    }

    if (data.outro) {
      let outroText = data.outro;
      if (genre === 'rap' && chopperMode) {
        outroText = convertToChopperStyle(data.outro);
      }
      formattedLyrics += `[Outro]\n${outroText}`;
    }
    
    output.textContent = formattedLyrics;

    const analysisData = await analyzeLyrics(formattedLyrics);
    if (analysisData) {
      humanityMeter.style.width = `${analysisData.humanityScore}%`;
      const meterLabel = document.querySelector('.meter-label');
      meterLabel.textContent = `${analysisData.humanityScore}% Human`;
      
      let analysisText = `${analysisData.analysis}\n\n`;
      analysisText += `Strengths:\n${analysisData.strengths.map(s => `• ${s}`).join('\n')}\n\n`;
      analysisText += `Suggestions for improvement:\n${analysisData.suggestions.map(s => `• ${s}`).join('\n')}`;
      analysis.textContent = analysisText;
    }

  } catch (error) {
    console.error('Error:', error);
    output.textContent = 'Sorry, something went wrong! Please try again.';
  } finally {
    loading.style.display = 'none';
  }
}

document.addEventListener('DOMContentLoaded', () => {
  const waves = document.querySelectorAll('.wave');
  waves.forEach((wave, index) => {
    wave.style.animationDelay = `${index * 0.2}s`;
  });
  toggleChopperSwitch();
});
</script>
</body></html>
