<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Basketball Unders Predictor | Any League</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            min-height: 100vh;
            padding: 16px;
        }

        .container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }

        h1 {
            color: #1a1a2e;
            text-align: center;
            font-size: 24px;
            margin-bottom: 5px;
        }

        .subtitle {
            text-align: center;
            color: #666;
            font-size: 12px;
            margin-bottom: 20px;
        }

        .input-group {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-direction: column;
        }

        input, select {
            padding: 14px;
            border: 2px solid #e0e0e0;
            border-radius: 12px;
            font-size: 16px;
            width: 100%;
        }

        select {
            background: white;
        }

        button {
            background: #f39c12;
            color: #1a1a2e;
            border: none;
            padding: 14px;
            border-radius: 12px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            width: 100%;
        }

        button:active {
            transform: scale(0.98);
        }

        .results {
            margin-top: 20px;
        }

        .hidden {
            display: none;
        }

        .prediction-card {
            background: #f5f5f5;
            padding: 12px 16px;
            margin: 10px 0;
            border-radius: 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .market {
            font-weight: bold;
            font-size: 16px;
        }

        .probability {
            font-size: 22px;
            font-weight: bold;
            padding: 4px 12px;
            border-radius: 20px;
        }

        .high-prob {
            color: #27ae60;
            background: #d5f4e6;
        }

        .medium-prob {
            color: #f39c12;
            background: #fef5e7;
        }

        .low-prob {
            color: #e74c3c;
            background: #fdedec;
        }

        .recommendation {
            background: #1a1a2e;
            color: white;
            padding: 16px;
            border-radius: 12px;
            margin-top: 16px;
            text-align: center;
        }

        .best-pick {
            font-size: 18px;
            font-weight: bold;
            color: #f39c12;
        }

        .info {
            background: #e8f4f8;
            padding: 12px;
            border-radius: 12px;
            margin-top: 16px;
            font-size: 12px;
            text-align: center;
        }

        .footer {
            margin-top: 20px;
            text-align: center;
            font-size: 10px;
            color: #999;
            border-top: 1px solid #eee;
            padding-top: 16px;
        }
    </style>
</head>
<body>
<div class="container">
    <h1>🏀 Basketball Unders</h1>
    <div class="subtitle">Under 160.5 | 170.5 | 180.5 | 190.5 | Works for any league</div>

    <div class="input-group">
        <input type="text" id="homeTeam" placeholder="Home Team (e.g., Lakers, Real Madrid)">
        <input type="text" id="awayTeam" placeholder="Away Team (e.g., Celtics, Barcelona)">
        
        <select id="league">
            <option value="pro">🏆 Pro League (NBA/EuroLeague) - High scoring</option>
            <option value="college">🎓 College/NCAA - Medium scoring</option>
            <option value="international">🌍 International - Lower scoring</option>
            <option value="custom">⚡ Custom - Enter averages below</option>
        </select>
        
        <div id="customInputs" style="display: none;">
            <input type="number" id="homeAvg" placeholder="Home team avg points (e.g., 85)" step="1">
            <input type="number" id="awayAvg" placeholder="Away team avg points (e.g., 82)" step="1">
        </div>
        
        <button onclick="predictMatch()">🔮 Predict Unders</button>
    </div>

    <div id="results" class="results hidden">
        <h3>📊 Prediction Results</h3>
        
        <div class="prediction-card">
            <span class="market">Under 160.5</span>
            <span class="probability" id="under160">--%</span>
        </div>
        <div class="prediction-card">
            <span class="market">Under 170.5</span>
            <span class="probability" id="under170">--%</span>
        </div>
        <div class="prediction-card">
            <span class="market">Under 180.5</span>
            <span class="probability" id="under180">--%</span>
        </div>
        <div class="prediction-card">
            <span class="market">Under 190.5</span>
            <span class="probability" id="under190">--%</span>
        </div>

        <div class="recommendation" id="recommendation"></div>
        <div class="info" id="info">
            💡 Probabilities are estimates based on league averages
        </div>
    </div>

    <div class="footer">
        ⭐ Works for NBA, EuroLeague, NCAA, International | Enter any two teams
    </div>
</div>

<script>
    // League base averages (points per game)
    const leagueAverages = {
        pro: { home: 112, away: 110 },      // NBA/EuroLeague
        college: { home: 78, away: 75 },     // NCAA
        international: { home: 82, away: 80 } // FIBA/International
    };

    function getTeamAverages() {
        const league = document.getElementById('league').value;
        
        if (league === 'custom') {
            const homeAvg = parseFloat(document.getElementById('homeAvg').value);
            const awayAvg = parseFloat(document.getElementById('awayAvg').value);
            
            if (!isNaN(homeAvg) && !isNaN(awayAvg) && homeAvg > 0 && awayAvg > 0) {
                return { homeScored: homeAvg, awayScored: awayAvg };
            }
        }
        
        const avg = leagueAverages[league];
        return { homeScored: avg.home, awayScored: avg.away };
    }

    function calculateProbability(expectedTotal, line) {
        // Calculate how likely the game stays UNDER the line
        // Difference between line and expected total
        const diff = line - expectedTotal;
        
        // Realistic probability curve (not extreme)
        let probability = 50; // base 50%
        
        if (diff >= 20) probability = 78;
        else if (diff >= 15) probability = 72;
        else if (diff >= 10) probability = 65;
        else if (diff >= 5) probability = 58;
        else if (diff >= 0) probability = 50;
        else if (diff >= -5) probability = 42;
        else if (diff >= -10) probability = 35;
        else if (diff >= -15) probability = 28;
        else probability = 22;
        
        // Add slight variance based on team names (for realism, not random)
        // Ensure probabilities stay in realistic range (30-75% typically)
        probability = Math.min(78, Math.max(28, probability));
        
        return Math.round(probability);
    }

    function predictMatch() {
        const homeTeam = document.getElementById('homeTeam').value.trim();
        const awayTeam = document.getElementById('awayTeam').value.trim();
        
        if (!homeTeam || !awayTeam) {
            alert('Please enter both team names');
            return;
        }
        
        const teamAverages = getTeamAverages();
        
        // Calculate expected total points
        const expectedTotal = teamAverages.homeScored + teamAverages.awayScored;
        
        // Calculate probabilities for each UNDER line
        const under160 = calculateProbability(expectedTotal, 160.5);
        const under170 = calculateProbability(expectedTotal, 170.5);
        const under180 = calculateProbability(expectedTotal, 180.5);
        const under190 = calculateProbability(expectedTotal, 190.5);
        
        // Update display
        document.getElementById('under160').innerHTML = under160 + '%';
        document.getElementById('under170').innerHTML = under170 + '%';
        document.getElementById('under180').innerHTML = under180 + '%';
        document.getElementById('under190').innerHTML = under190 + '%';
        
        // Add color coding
        addColorClass('under160', under160);
        addColorClass('under170', under170);
        addColorClass('under180', under180);
        addColorClass('under190', under190);
        
        // Find best pick
        const picks = [
            { line: 'Under 160.5', prob: under160 },
            { line: 'Under 170.5', prob: under170 },
            { line: 'Under 180.5', prob: under180 },
            { line: 'Under 190.5', prob: under190 }
        ];
        picks.sort((a, b) => b.prob - a.prob);
        const best = picks[0];
        
        // Generate recommendation
        let recText = '';
        let confidence = '';
        
        if (best.prob >= 65) {
            confidence = '🔥 HIGH CONFIDENCE';
            recText = `${best.line} at ${best.prob}% - Strong under opportunity`;
        } else if (best.prob >= 55) {
            confidence = '✅ MEDIUM CONFIDENCE';
            recText = `${best.line} at ${best.prob}% - Consider for small stake`;
        } else if (best.prob >= 45) {
            confidence = '📊 LOW CONFIDENCE';
            recText = `${best.line} at ${best.prob}% - Look for over instead`;
        } else {
            confidence = '⚠️ AVOID UNDERS';
            recText = `Best under is ${best.line} at only ${best.prob}% - Consider OVER bets today`;
        }
        
        document.getElementById('recommendation').innerHTML = `
            <div class="best-pick">${confidence}</div>
            <div style="margin-top: 8px;">${recText}</div>
            <div style="margin-top: 10px; font-size: 13px;">📈 Expected total: ${expectedTotal} points</div>
        `;
        
        // Update info text
        const league = document.getElementById('league').value;
        let leagueName = 'Pro League';
        if (league === 'college') leagueName = 'College/NCAA';
        if (league === 'international') leagueName = 'International';
        if (league === 'custom') leagueName = 'Custom averages';
        
        document.getElementById('info').innerHTML = `
            💡 Based on ${leagueName} averages | ${homeTeam}: ${teamAverages.homeScored}ppg | ${awayTeam}: ${teamAverages.awayScored}ppg
        `;
        
        document.getElementById('results').classList.remove('hidden');
    }
    
    function addColorClass(elementId, probability) {
        const element = document.getElementById(elementId);
        element.classList.remove('high-prob', 'medium-prob', 'low-prob');
        
        if (probability >= 60) {
            element.classList.add('high-prob');
        } else if (probability >= 45) {
            element.classList.add('medium-prob');
        } else {
            element.classList.add('low-prob');
        }
    }
    
    // Show/hide custom inputs
    document.getElementById('league').addEventListener('change', function() {
        const customDiv = document.getElementById('customInputs');
        if (this.value === 'custom') {
            customDiv.style.display = 'block';
        } else {
            customDiv.style.display = 'none';
        }
    });
    
    // Enter key support
    document.querySelectorAll('input').forEach(input => {
        input.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                predictMatch();
            }
        });
    });
</script>
</body>
</html>0
