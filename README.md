# Ob-Havo
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>O'zbekiston Ob-havo</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&family=Space+Mono:wght@400;700&display=swap');
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        :root {
            --bg-primary: #0f172a;
            --bg-secondary: #1e293b;
            --bg-card: rgba(30, 41, 59, 0.7);
            --text-primary: #f1f5f9;
            --text-secondary: #94a3b8;
            --accent: #3b82f6;
            --accent-glow: rgba(59, 130, 246, 0.3);
            --sunny: #fbbf24;
            --rainy: #60a5fa;
            --cloudy: #94a3b8;
            --snowy: #e0f2fe;
        }
        
        body {
            font-family: 'Outfit', sans-serif;
            background: var(--bg-primary);
            color: var(--text-primary);
            min-height: 100vh;
            overflow-x: hidden;
            position: relative;
        }
        
        /* Animatsiyali fon */
        .weather-bg {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            overflow: hidden;
            pointer-events: none;
        }
        
        /* Quyosh animatsiyasi */
        .sun {
            position: absolute;
            top: 5%;
            right: 5%;
            width: 120px;
            height: 120px;
            background: radial-gradient(circle, #fbbf24 0%, #f59e0b 80%);
            border-radius: 50%;
            box-shadow: 0 0 50px #fbbf24, 0 0 100px #f59e0b, 0 0 150px #fbbf24;
            animation: sunPulse 4s ease-in-out infinite, sunRotate 20s linear infinite;
            opacity: 0.9;
        }
        
        .sun::after {
            content: '';
            position: absolute;
            top: -10px;
            left: -10px;
            right: -10px;
            bottom: -10px;
            background: radial-gradient(circle, rgba(251, 191, 36, 0.4) 0%, transparent 70%);
            border-radius: 50%;
            animation: sunGlow 3s ease-in-out infinite;
        }
        
        @keyframes sunPulse {
            0%, 100% { transform: scale(1); opacity: 0.9; }
            50% { transform: scale(1.1); opacity: 1; }
        }
        
        @keyframes sunRotate {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        @keyframes sunGlow {
            0%, 100% { opacity: 0.3; }
            50% { opacity: 0.6; }
        }
        
        /* Bulutlar */
        .cloud {
            position: absolute;
            background: rgba(255, 255, 255, 0.8);
            border-radius: 80px;
            filter: blur(2px);
            animation: cloudMove 30s linear infinite;
        }
        
        .cloud::before, .cloud::after {
            content: '';
            position: absolute;
            background: inherit;
            border-radius: 50%;
        }
        
        .cloud1 {
            width: 120px;
            height: 40px;
            top: 15%;
            left: -150px;
            animation-duration: 35s;
        }
        
        .cloud1::before {
            width: 50px;
            height: 50px;
            top: -25px;
            left: 20px;
        }
        
        .cloud1::after {
            width: 70px;
            height: 60px;
            top: -30px;
            left: 50px;
        }
        
        .cloud2 {
            width: 180px;
            height: 60px;
            top: 25%;
            left: -200px;
            animation-duration: 45s;
            animation-delay: -10s;
        }
        
        .cloud2::before {
            width: 80px;
            height: 80px;
            top: -40px;
            left: 30px;
        }
        
        .cloud2::after {
            width: 100px;
            height: 90px;
            top: -50px;
            left: 80px;
        }
        
        .cloud3 {
            width: 150px;
            height: 50px;
            top: 45%;
            left: -180px;
            animation-duration: 40s;
            animation-delay: -5s;
        }
        
        .cloud3::before {
            width: 70px;
            height: 70px;
            top: -35px;
            left: 20px;
        }
        
        .cloud3::after {
            width: 90px;
            height: 80px;
            top: -40px;
            left: 60px;
        }
        
        @keyframes cloudMove {
            0% { transform: translateX(0); }
            100% { transform: translateX(calc(100vw + 300px)); }
        }
        
        /* Yomg'ir animatsiyasi */
        .rain-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            opacity: 0;
            transition: opacity 0.5s;
        }
        
        .rain-container.active {
            opacity: 0.7;
        }
        
        .rain-drop {
            position: absolute;
            width: 2px;
            height: 20px;
            background: linear-gradient(to bottom, transparent, #60a5fa, #3b82f6);
            animation: rainFall 0.8s linear infinite;
        }
        
        @keyframes rainFall {
            0% { transform: translateY(-100px); }
            100% { transform: translateY(100vh); }
        }
        
        /* Qor animatsiyasi */
        .snow-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            opacity: 0;
            transition: opacity 0.5s;
        }
        
        .snow-container.active {
            opacity: 0.9;
        }
        
        .snowflake {
            position: absolute;
            color: white;
            font-size: 24px;
            filter: drop-shadow(0 0 5px rgba(255, 255, 255, 0.5));
            animation: snowFall 10s linear infinite;
        }
        
        @keyframes snowFall {
            0% { transform: translateY(-100px) rotate(0deg); }
            100% { transform: translateY(100vh) rotate(360deg); }
        }
        
        /* Tuman animatsiyasi */
        .fog-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            opacity: 0;
            transition: opacity 0.5s;
            pointer-events: none;
        }
        
        .fog-container.active {
            opacity: 0.3;
            animation: fogWave 10s ease-in-out infinite;
        }
        
        @keyframes fogWave {
            0%, 100% { backdrop-filter: blur(5px); }
            50% { backdrop-filter: blur(15px); }
        }
        
        /* Asosiy container */
        .container {
            position: relative;
            z-index: 10;
            max-width: 500px;
            margin: 0 auto;
            padding: 16px;
        }
        
        /* Header */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 16px 20px;
            background: var(--bg-card);
            backdrop-filter: blur(20px);
            border-radius: 30px;
            margin-bottom: 20px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
            animation: slideDown 0.6s ease-out;
            transform-origin: top;
        }
        
        @keyframes slideDown {
            0% { transform: translateY(-100px) scale(0.8); opacity: 0; }
            100% { transform: translateY(0) scale(1); opacity: 1; }
        }
        
        .logo {
            font-size: 24px;
            font-weight: 700;
            background: linear-gradient(135deg, #3b82f6, #fbbf24);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            animation: logoGlow 2s ease-in-out infinite;
        }
        
        @keyframes logoGlow {
            0%, 100% { filter: drop-shadow(0 0 5px #3b82f6); }
            50% { filter: drop-shadow(0 0 20px #fbbf24); }
        }
        
        .controls {
            display: flex;
            gap: 10px;
        }
        
        .btn {
            background: var(--bg-secondary);
            border: 1px solid rgba(255, 255, 255, 0.1);
            padding: 12px 18px;
            border-radius: 20px;
            color: var(--text-primary);
            font-size: 20px;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
        }
        
        .btn:hover {
            transform: translateY(-3px) scale(1.05);
            box-shadow: 0 8px 24px rgba(59, 130, 246, 0.3);
            border-color: var(--accent);
        }
        
        .btn:active {
            transform: translateY(0) scale(0.95);
        }
        
        /* Region selector */
        .region-selector {
            margin-bottom: 20px;
            animation: fadeInUp 0.6s ease-out 0.2s both;
        }
        
        @keyframes fadeInUp {
            0% { transform: translateY(30px); opacity: 0; }
            100% { transform: translateY(0); opacity: 1; }
        }
        
        .region-selector select {
            width: 100%;
            padding: 16px 24px;
            background: var(--bg-card);
            backdrop-filter: blur(20px);
            border: 2px solid transparent;
            border-radius: 30px;
            color: var(--text-primary);
            font-size: 16px;
            font-weight: 500;
            cursor: pointer;
            outline: none;
            transition: all 0.3s ease;
            appearance: none;
            background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24' stroke='%233b82f6'%3E%3Cpath stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='M19 9l-7 7-7-7'%3E%3C/path%3E%3C/svg%3E");
            background-repeat: no-repeat;
            background-position: right 20px center;
            background-size: 24px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
        }
        
        .region-selector select:focus {
            border-color: var(--accent);
            box-shadow: 0 0 0 4px var(--accent-glow), 0 8px 32px rgba(0, 0, 0, 0.3);
        }
        
        .region-selector select option {
            background: var(--bg-secondary);
            color: var(--text-primary);
            padding: 12px;
        }
        
        /* Bugungi ob-havo */
        .current-weather {
            background: var(--bg-card);
            backdrop-filter: blur(20px);
            padding: 28px;
            border-radius: 40px;
            margin-bottom: 20px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
            animation: scaleIn 0.6s ease-out 0.3s both;
            transform-origin: center;
        }
        
        @keyframes scaleIn {
            0% { transform: scale(0.9); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }
        
        .location-info {
            display: flex;
            align-items: center;
            gap: 8px;
            margin-bottom: 20px;
            color: var(--text-secondary);
            font-size: 18px;
            padding-bottom: 16px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .location-info span:first-child {
            animation: bounce 2s ease-in-out infinite;
        }
        
        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-3px); }
        }
        
        .main-weather {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 16px;
        }
        
        .temperature {
            font-size: 80px;
            font-weight: 700;
            font-family: 'Space Mono', monospace;
            background: linear-gradient(135deg, #fff, #94a3b8);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            line-height: 1;
            animation: tempPulse 2s ease-in-out infinite;
        }
        
        @keyframes tempPulse {
            0%, 100% { text-shadow: 0 0 20px rgba(255, 255, 255, 0.3); }
            50% { text-shadow: 0 0 40px rgba(59, 130, 246, 0.5); }
        }
        
        .weather-icon {
            font-size: 90px;
            filter: drop-shadow(0 10px 20px rgba(0, 0, 0, 0.3));
            animation: floatIcon 3s ease-in-out infinite;
        }
        
        @keyframes floatIcon {
            0%, 100% { transform: translateY(0) rotate(0deg); }
            50% { transform: translateY(-5px) rotate(5deg); }
        }
        
        .weather-desc {
            font-size: 24px;
            font-weight: 500;
            color: var(--text-secondary);
            margin-bottom: 24px;
            text-transform: capitalize;
            padding-bottom: 16px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        /* Quyosh vaqti */
        .sun-times {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 16px;
            margin-bottom: 24px;
            padding: 16px;
            background: rgba(0, 0, 0, 0.2);
            border-radius: 30px;
            border: 1px solid rgba(255, 255, 255, 0.05);
        }
        
        .sun-item {
            display: flex;
            align-items: center;
            gap: 12px;
            animation: slideInLeft 0.5s ease-out;
        }
        
        .sun-item:last-child {
            animation: slideInRight 0.5s ease-out;
        }
        
        @keyframes slideInLeft {
            0% { transform: translateX(-20px); opacity: 0; }
            100% { transform: translateX(0); opacity: 1; }
        }
        
        @keyframes slideInRight {
            0% { transform: translateX(20px); opacity: 0; }
            100% { transform: translateX(0); opacity: 1; }
        }
        
        .sun-icon {
            font-size: 36px;
            animation: sunRotate 10s linear infinite;
        }
        
        .sun-info {
            display: flex;
            flex-direction: column;
        }
        
        .sun-label {
            font-size: 14px;
            color: var(--text-secondary);
        }
        
        .sun-time {
            font-size: 22px;
            font-weight: 600;
            font-family: 'Space Mono', monospace;
        }
        
        /* Detallar grid */
        .details-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 12px;
        }
        
        .detail-card {
            background: rgba(0, 0, 0, 0.2);
            padding: 18px;
            border-radius: 24px;
            border: 1px solid rgba(255, 255, 255, 0.05);
            transition: all 0.3s ease;
            animation: cardAppear 0.5s ease-out;
            animation-fill-mode: both;
        }
        
        .detail-card:nth-child(1) { animation-delay: 0.1s; }
        .detail-card:nth-child(2) { animation-delay: 0.2s; }
        .detail-card:nth-child(3) { animation-delay: 0.3s; }
        .detail-card:nth-child(4) { animation-delay: 0.4s; }
        .detail-card:nth-child(5) { animation-delay: 0.5s; }
        .detail-card:nth-child(6) { animation-delay: 0.6s; }
        
        @keyframes cardAppear {
            0% { transform: scale(0.8); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }
        
        .detail-card:hover {
            transform: translateY(-3px) scale(1.02);
            background: rgba(59, 130, 246, 0.1);
            border-color: var(--accent);
        }
        
        .detail-label {
            display: flex;
            align-items: center;
            gap: 8px;
            color: var(--text-secondary);
            font-size: 14px;
            margin-bottom: 8px;
        }
        
        .detail-value {
            font-size: 26px;
            font-weight: 600;
            font-family: 'Space Mono', monospace;
        }
        
        .detail-unit {
            font-size: 14px;
            color: var(--text-secondary);
            margin-left: 4px;
        }
        
        /* Progress bar */
        .progress-bar {
            width: 100%;
            height: 8px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            margin-top: 10px;
            overflow: hidden;
        }
        
        .progress-fill {
            height: 100%;
            background: linear-gradient(90deg, #3b82f6, #fbbf24);
            border-radius: 10px;
            transition: width 0.5s cubic-bezier(0.4, 0, 0.2, 1);
            animation: progressPulse 2s ease-in-out infinite;
        }
        
        @keyframes progressPulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.8; }
        }
        
        /* Quyosh nurlanishi */
        .solar-card {
            background: linear-gradient(135deg, rgba(245, 158, 11, 0.1), rgba(251, 191, 36, 0.2));
            border: 1px solid #f59e0b40;
            grid-column: span 2;
        }
        
        .solar-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-top: 12px;
        }
        
        .solar-item {
            text-align: center;
            padding: 12px;
            background: rgba(0, 0, 0, 0.2);
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.05);
            transition: all 0.3s ease;
        }
        
        .solar-item:hover {
            background: rgba(245, 158, 11, 0.2);
            transform: scale(1.05);
        }
        
        .solar-value {
            font-size: 22px;
            font-weight: 700;
            color: #fbbf24;
            font-family: 'Space Mono', monospace;
        }
        
        /* Forecast section */
        .forecast-section {
            margin-top: 24px;
            animation: fadeInUp 0.6s ease-out 0.5s both;
        }
        
        .section-title {
            font-size: 22px;
            font-weight: 600;
            margin-bottom: 16px;
            padding-left: 8px;
            background: linear-gradient(135deg, #3b82f6, #fbbf24);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        
        .forecast-list {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }
        
        .forecast-item {
            display: grid;
            grid-template-columns: 120px 1fr auto;
            align-items: center;
            background: var(--bg-card);
            backdrop-filter: blur(20px);
            padding: 18px 22px;
            border-radius: 30px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            animation: slideInRight 0.5s ease-out;
            animation-fill-mode: both;
        }
        
        .forecast-item:nth-child(1) { animation-delay: 0.1s; }
        .forecast-item:nth-child(2) { animation-delay: 0.2s; }
        .forecast-item:nth-child(3) { animation-delay: 0.3s; }
        .forecast-item:nth-child(4) { animation-delay: 0.4s; }
        .forecast-item:nth-child(5) { animation-delay: 0.5s; }
        .forecast-item:nth-child(6) { animation-delay: 0.6s; }
        .forecast-item:nth-child(7) { animation-delay: 0.7s; }
        .forecast-item:nth-child(8) { animation-delay: 0.8s; }
        .forecast-item:nth-child(9) { animation-delay: 0.9s; }
        .forecast-item:nth-child(10) { animation-delay: 1s; }
        .forecast-item:nth-child(11) { animation-delay: 1.1s; }
        .forecast-item:nth-child(12) { animation-delay: 1.2s; }
        .forecast-item:nth-child(13) { animation-delay: 1.3s; }
        .forecast-item:nth-child(14) { animation-delay: 1.4s; }
        .forecast-item:nth-child(15) { animation-delay: 1.5s; }
        .forecast-item:nth-child(16) { animation-delay: 1.6s; }
        
        @keyframes slideInRight {
            0% { transform: translateX(-30px); opacity: 0; }
            100% { transform: translateX(0); opacity: 1; }
        }
        
        .forecast-item:hover {
            transform: translateX(8px) scale(1.02);
            background: rgba(59, 130, 246, 0.15);
            border-color: var(--accent);
            box-shadow: 0 10px 30px rgba(59, 130, 246, 0.2);
        }
        
        .forecast-day {
            font-weight: 600;
            font-size: 16px;
        }
        
        .forecast-date {
            font-size: 13px;
            color: var(--text-secondary);
            margin-top: 4px;
        }
        
        .forecast-icon {
            font-size: 40px;
            text-align: center;
            filter: drop-shadow(0 5px 10px rgba(0, 0, 0, 0.3));
            animation: floatIcon 3s ease-in-out infinite;
        }
        
        .forecast-temp {
            font-size: 22px;
            font-weight: 600;
            font-family: 'Space Mono', monospace;
        }
        
        .forecast-min {
            color: var(--text-secondary);
            font-size: 18px;
            margin-left: 8px;
        }
        
        .weather-tag {
            display: inline-block;
            padding: 4px 10px;
            background: linear-gradient(135deg, rgba(59, 130, 246, 0.2), rgba(251, 191, 36, 0.2));
            border-radius: 20px;
            font-size: 12px;
            margin-left: 12px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        /* Loading */
        .loading {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 60vh;
            gap: 24px;
        }
        
        .spinner {
            width: 70px;
            height: 70px;
            border: 5px solid var(--bg-secondary);
            border-top-color: #3b82f6;
            border-right-color: #fbbf24;
            border-bottom-color: #60a5fa;
            border-left-color: #f59e0b;
            border-radius: 50%;
            animation: spin 1s cubic-bezier(0.68, -0.55, 0.265, 1.55) infinite;
            box-shadow: 0 0 30px rgba(59, 130, 246, 0.3);
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(720deg); }
        }
        
        .loading p {
            font-size: 18px;
            color: var(--text-secondary);
            animation: pulse 1.5s ease-in-out infinite;
        }
        
        @keyframes pulse {
            0%, 100% { opacity: 0.7; }
            50% { opacity: 1; }
        }
        
        /* Error */
        .error {
            text-align: center;
            padding: 50px 20px;
            background: rgba(239, 68, 68, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 40px;
            border: 1px solid rgba(239, 68, 68, 0.3);
            animation: shake 0.5s ease-out;
        }
        
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-10px); }
            75% { transform: translateX(10px); }
        }
        
        .error h3 {
            color: #ef4444;
            margin-bottom: 12px;
            font-size: 24px;
        }
        
        /* Responsive */
        @media (max-width: 480px) {
            .temperature {
                font-size: 70px;
            }
            
            .weather-icon {
                font-size: 80px;
            }
            
            .details-grid {
                grid-template-columns: 1fr;
            }
            
            .solar-card {
                grid-column: span 1;
            }
            
            .solar-grid {
                grid-template-columns: 1fr;
            }
            
            .forecast-item {
                grid-template-columns: 100px 1fr;
                gap: 10px;
            }
            
            .weather-tag {
                display: none;
            }
        }
        
        /* Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
        }
        
        ::-webkit-scrollbar-track {
            background: var(--bg-secondary);
        }
        
        ::-webkit-scrollbar-thumb {
            background: linear-gradient(135deg, #3b82f6, #fbbf24);
            border-radius: 10px;
        }
        
        ::-webkit-scrollbar-thumb:hover {
            background: linear-gradient(135deg, #fbbf24, #3b82f6);
        }
    </style>
</head>
<body>
    <!-- Animatsiyali fon -->
    <div class="weather-bg" id="weatherBg">
        <div class="sun" id="sun"></div>
        <div class="cloud cloud1" id="cloud1"></div>
        <div class="cloud cloud2" id="cloud2"></div>
        <div class="cloud cloud3" id="cloud3"></div>
        <div class="rain-container" id="rainContainer"></div>
        <div class="snow-container" id="snowContainer"></div>
        <div class="fog-container" id="fogContainer"></div>
    </div>

    <div class="container">
        <!-- Header -->
        <div class="header">
            <div class="logo">🌤 O'BEKISTON OB-HAVO</div>
            <div class="controls">
                <button class="btn" id="themeBtn" title="Tungi rejim">🌙</button>
                <button class="btn" id="refreshBtn" title="Yangilash">🔄</button>
            </div>
        </div>

        <!-- Region selector -->
        <div class="region-selector">
            <select id="regionSelect">
                <option value="">🌍 Viloyatni tanlang...</option>
                <option value="Toshkent" data-lat="41.3111" data-lon="69.2797">📍 Toshkent</option>
                <option value="Samarqand" data-lat="39.6542" data-lon="66.9597">📍 Samarqand</option>
                <option value="Buxoro" data-lat="39.7747" data-lon="64.4286">📍 Buxoro</option>
                <option value="Xiva" data-lat="41.3765" data-lon="60.3620">📍 Xiva</option>
                <option value="Andijon" data-lat="40.7821" data-lon="72.3442">📍 Andijon</option>
                <option value="Farg'ona" data-lat="40.3864" data-lon="71.7864">📍 Farg'ona</option>
                <option value="Namangan" data-lat="40.9983" data-lon="71.6726">📍 Namangan</option>
                <option value="Qarshi" data-lat="38.8606" data-lon="65.7978">📍 Qarshi</option>
                <option value="Termiz" data-lat="37.2242" data-lon="67.2783">📍 Termiz</option>
                <option value="Jizzax" data-lat="40.1158" data-lon="67.8422">📍 Jizzax</option>
                <option value="Guliston" data-lat="40.4897" data-lon="68.7842">📍 Guliston</option>
                <option value="Navoiy" data-lat="40.0844" data-lon="65.3792">📍 Navoiy</option>
                <option value="Nukus" data-lat="42.4619" data-lon="59.6167">📍 Nukus</option>
                <option value="Urganch" data-lat="41.5515" data-lon="60.6275">📍 Urganch</option>
            </select>
        </div>

        <!-- Content -->
        <div id="content">
            <div class="loading">
                <div class="spinner"></div>
                <p>Ma'lumotlar yuklanmoqda...</p>
            </div>
        </div>
    </div>

    <script>
        // Telegram Web App
        const tg = window.Telegram.WebApp;
        tg.expand();
        
        // API kaliti
        const API_KEY = '81e25b11405d011ee85dc8b197d38989';
        
        const weekDays = ['Yakshanba', 'Dushanba', 'Seshanba', 'Chorshanba', 'Payshanba', 'Juma', 'Shanba'];
        
        const weatherIcons = {
            'Clear': '☀️',
            'Clouds': '☁️',
            'Rain': '🌧️',
            'Drizzle': '🌦️',
            'Thunderstorm': '⛈️',
            'Snow': '❄️',
            'Mist': '🌫️',
            'Smoke': '🌫️',
            'Haze': '🌫️',
            'Dust': '🌫️',
            'Fog': '🌫️',
            'default': '🌤️'
        };

        let currentRegion = null;
        let isDarkMode = true;

        // Event listeners
        document.getElementById('regionSelect').addEventListener('change', handleRegionChange);
        document.getElementById('refreshBtn').addEventListener('click', refreshWeather);
        document.getElementById('themeBtn').addEventListener('click', toggleTheme);

        function toggleTheme() {
            isDarkMode = !isDarkMode;
            if (isDarkMode) {
                document.documentElement.style.setProperty('--bg-primary', '#0f172a');
                document.documentElement.style.setProperty('--bg-secondary', '#1e293b');
                document.documentElement.style.setProperty('--text-primary', '#f1f5f9');
                document.documentElement.style.setProperty('--text-secondary', '#94a3b8');
                document.getElementById('themeBtn').textContent = '🌙';
            } else {
                document.documentElement.style.setProperty('--bg-primary', '#e0f2fe');
                document.documentElement.style.setProperty('--bg-secondary', '#bae6fd');
                document.documentElement.style.setProperty('--text-primary', '#0c4a6e');
                document.documentElement.style.setProperty('--text-secondary', '#0369a1');
                document.getElementById('themeBtn').textContent = '☀️';
            }
        }

        function refreshWeather() {
            if (currentRegion) {
                const selected = document.getElementById('regionSelect').selectedOptions[0];
                fetchAllWeatherData(selected.dataset.lat, selected.dataset.lon, selected.value);
            }
        }

        function handleRegionChange() {
            const select = document.getElementById('regionSelect');
            const selected = select.options[select.selectedIndex];
            
            if (selected.value) {
                currentRegion = selected.value;
                fetchAllWeatherData(selected.dataset.lat, selected.dataset.lon, selected.value);
            }
        }

        // Fonni ob-havoga moslash
        function updateWeatherBackground(weatherMain) {
            const sun = document.getElementById('sun');
            const clouds = [document.getElementById('cloud1'), document.getElementById('cloud2'), document.getElementById('cloud3')];
            const rainContainer = document.getElementById('rainContainer');
            const snowContainer = document.getElementById('snowContainer');
            const fogContainer = document.getElementById('fogContainer');
            
            // Hammasini yashirish
            sun.style.display = 'none';
            clouds.forEach(c => c.style.display = 'none');
            rainContainer.classList.remove('active');
            snowContainer.classList.remove('active');
            fogContainer.classList.remove('active');
            
            // Tozalash
            rainContainer.innerHTML = '';
            snowContainer.innerHTML = '';
            
            if (weatherMain === 'Clear') {
                sun.style.display = 'block';
                clouds.forEach(c => c.style.display = 'none');
            } else if (weatherMain === 'Clouds') {
                sun.style.display = 'none';
                clouds.forEach(c => c.style.display = 'block');
            } else if (['Rain', 'Drizzle', 'Thunderstorm'].includes(weatherMain)) {
                sun.style.display = 'none';
                clouds.forEach(c => c.style.display = 'block');
                rainContainer.classList.add('active');
                createRain();
            } else if (weatherMain === 'Snow') {
                sun.style.display = 'none';
                clouds.forEach(c => c.style.display = 'block');
                snowContainer.classList.add('active');
                createSnow();
            } else if (['Mist', 'Smoke', 'Haze', 'Fog'].includes(weatherMain)) {
                sun.style.display = 'none';
                clouds.forEach(c => c.style.display = 'block');
                fogContainer.classList.add('active');
            }
        }

        function createRain() {
            const rainContainer = document.getElementById('rainContainer');
            for (let i = 0; i < 100; i++) {
                const drop = document.createElement('div');
                drop.className = 'rain-drop';
                drop.style.left = Math.random() * 100 + '%';
                drop.style.animationDelay = Math.random() * 2 + 's';
                drop.style.animationDuration = (0.5 + Math.random() * 0.5) + 's';
                drop.style.opacity = 0.3 + Math.random() * 0.5;
                rainContainer.appendChild(drop);
            }
        }

        function createSnow() {
            const snowContainer = document.getElementById('snowContainer');
            for (let i = 0; i < 50; i++) {
                const flake = document.createElement('div');
                flake.className = 'snowflake';
                flake.textContent = '❄️';
                flake.style.left = Math.random() * 100 + '%';
                flake.style.animationDelay = Math.random() * 5 + 's';
                flake.style.animationDuration = (5 + Math.random() * 5) + 's';
                flake.style.fontSize = (15 + Math.random() * 15) + 'px';
                flake.style.opacity = 0.3 + Math.random() * 0.5;
                snowContainer.appendChild(flake);
            }
        }

        // Barcha ma'lumotlarni olish
        async function fetchAllWeatherData(lat, lon, city) {
            try {
                showLoading();
                
                // Hozirgi ob-havo
                const currentResponse = await fetch(
                    `https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lon}&exclude=minutely,alerts&appid=${API_KEY}&units=metric&lang=uz`
                );
                
                // 16 kunlik prognoz
                const dailyResponse = await fetch(
                    `https://api.openweathermap.org/data/2.5/forecast/daily?lat=${lat}&lon=${lon}&cnt=16&appid=${API_KEY}&units=metric&lang=uz`
                );
                
                // Quyosh nurlanishi
                const today = new Date().toISOString().split('T')[0];
                const solarResponse = await fetch(
                    `https://api.openweathermap.org/energy/2.0/solar/interval_data?lat=${lat}&lon=${lon}&date=${today}&interval=day&appid=${API_KEY}`
                );
                
                if (!currentResponse.ok) throw new Error('Hozirgi ob-havo ma\'lumotlari olinmadi');
                
                const currentData = await currentResponse.json();
                let dailyData = null;
                let solarData = null;
                
                if (dailyResponse.ok) {
                    dailyData = await dailyResponse.json();
                }
                
                if (solarResponse.ok) {
                    solarData = await solarResponse.json();
                }
                
                displayAllWeather(currentData, dailyData, solarData, city);
                
            } catch (error) {
                console.error('Xatolik:', error);
                showDemoWeather(city, lat, lon);
            }
        }

        // Ma'lumotlarni ko'rsatish
        function displayAllWeather(current, daily, solar, cityName) {
            const currentW = current.current;
            const weatherMain = currentW.weather[0].main;
            const icon = weatherIcons[weatherMain] || weatherIcons.default;
            
            // Fonni yangilash
            updateWeatherBackground(weatherMain);
            
            // Quyosh vaqtlari
            const sunrise = new Date(currentW.sunrise * 1000).toLocaleTimeString('uz-UZ', { 
                hour: '2-digit', 
                minute: '2-digit',
                hour12: false 
            });
            
            const sunset = new Date(currentW.sunset * 1000).toLocaleTimeString('uz-UZ', { 
                hour: '2-digit', 
                minute: '2-digit',
                hour12: false 
            });
            
            // Quyosh nurlanishi
            const solarHtml = solar?.data ? `
                <div class="detail-card solar-card">
                    <div class="detail-label">
                        <span>☀️</span>
                        <span>Quyosh nurlanishi</span>
                    </div>
                    <div class="solar-grid">
                        <div class="solar-item">
                            <div style="font-size: 12px; color: var(--text-secondary);">Jami</div>
                            <div class="solar-value">${solar.data.total || 0} <span style="font-size: 12px;">W/m²</span></div>
                        </div>
                        <div class="solar-item">
                            <div style="font-size: 12px; color: var(--text-secondary);">O'rtacha</div>
                            <div class="solar-value">${solar.data.average || 0} <span style="font-size: 12px;">W/m²</span></div>
                        </div>
                        <div class="solar-item">
                            <div style="font-size: 12px; color: var(--text-secondary);">Eng yuqori</div>
                            <div class="solar-value">${solar.data.max || 0} <span style="font-size: 12px;">W/m²</span></div>
                        </div>
                    </div>
                </div>
            ` : '';
            
            // 16 kunlik prognoz
            const forecastHtml = daily?.list ? daily.list.map((day, index) => {
                const date = new Date(day.dt * 1000);
                return `
                    <div class="forecast-item">
                        <div>
                            <div class="forecast-day">${weekDays[date.getDay()]}</div>
                            <div class="forecast-date">${date.toLocaleDateString('uz-UZ', { day: 'numeric', month: 'short' })}</div>
                        </div>
                        <div class="forecast-icon">${weatherIcons[day.weather[0].main] || '🌤️'}</div>
                        <div>
                            <span class="forecast-temp">${Math.round(day.temp.max)}°</span>
                            <span class="forecast-min">${Math.round(day.temp.min)}°</span>
                            <span class="weather-tag">${day.weather[0].description}</span>
                        </div>
                    </div>
                `;
            }).join('') : '<p style="color: var(--text-secondary); text-align: center;">16 kunlik prognoz mavjud emas</p>';
            
            const content = `
                <div class="current-weather">
                    <div class="location-info">
                        <span>📍</span>
                        <span>${cityName}, O'zbekiston</span>
                        <span style="margin-left: auto; font-size: 14px; background: rgba(255,255,255,0.1); padding: 4px 12px; border-radius: 20px;">
                            ${new Date().toLocaleDateString('uz-UZ', { day: 'numeric', month: 'long' })}
                        </span>
                    </div>
                    
                    <div class="main-weather">
                        <div class="temperature">${Math.round(currentW.temp)}°C</div>
                        <div class="weather-icon">${icon}</div>
                    </div>
                    
                    <div class="weather-desc">
                        ${currentW.weather[0].description}
                    </div>
                    
                    <div class="sun-times">
                        <div class="sun-item">
                            <span class="sun-icon">🌅</span>
                            <div class="sun-info">
                                <span class="sun-label">Quyosh chiqishi</span>
                                <span class="sun-time">${sunrise}</span>
                            </div>
                        </div>
                        <div class="sun-item">
                            <span class="sun-icon">🌇</span>
                            <div class="sun-info">
                                <span class="sun-label">Quyosh botishi</span>
                                <span class="sun-time">${sunset}</span>
                            </div>
                        </div>
                    </div>
                    
                    <div class="details-grid">
                        <div class="detail-card">
                            <div class="detail-label">
                                <span>🌡️</span>
                                <span>Seziladi</span>
                            </div>
                            <div class="detail-value">${Math.round(currentW.feels_like)}<span class="detail-unit">°C</span></div>
                        </div>
                        
                        <div class="detail-card">
                            <div class="detail-label">
                                <span>💧</span>
                                <span>Namlik</span>
                            </div>
                            <div class="detail-value">${currentW.humidity}<span class="detail-unit">%</span></div>
                            <div class="progress-bar">
                                <div class="progress-fill" style="width: ${currentW.humidity}%"></div>
                            </div>
                        </div>
                        
                        <div class="detail-card">
                            <div class="detail-label">
                                <span>💨</span>
                                <span>Shamol</span>
                            </div>
                            <div class="detail-value">${Math.round(currentW.wind_speed * 3.6)}<span class="detail-unit">km/h</span></div>
                        </div>
                        
                        <div class="detail-card">
                            <div class="detail-label">
                                <span>📊</span>
                                <span>Bosim</span>
                            </div>
                            <div class="detail-value">${currentW.pressure}<span class="detail-unit">hPa</span></div>
                        </div>
                        
                        <div class="detail-card">
                            <div class="detail-label">
                                <span>☀️</span>
                                <span>UV indeks</span>
                            </div>
                            <div class="detail-value">${currentW.uvi || 0}</div>
                        </div>
                        
                        <div class="detail-card">
                            <div class="detail-label">
                                <span>👁️</span>
                                <span>Ko'rinish</span>
                            </div>
                            <div class="detail-value">${(currentW.visibility / 1000).toFixed(1)}<span class="detail-unit">km</span></div>
                        </div>
                        
                        ${solarHtml}
                    </div>
                </div>
                
                <div class="forecast-section">
                    <h3 class="section-title">📅 16 KUNLIK PROGNOZ</h3>
                    <div class="forecast-list">
                        ${forecastHtml}
                    </div>
                </div>
            `;
            
            document.getElementById('content').innerHTML = content;
        }

        // Demo ma'lumotlar
        function showDemoWeather(city, lat, lon) {
            const demoCurrent = {
                current: {
                    temp: 23,
                    feels_like: 22,
                    humidity: 55,
                    wind_speed: 3.5,
                    pressure: 1015,
                    uvi: 5,
                    visibility: 10000,
                    sunrise: Date.now() / 1000 - 3600,
                    sunset: Date.now() / 1000 + 3600 * 8,
                    weather: [{ main: 'Clear', description: 'ochiq havo' }]
                }
            };
            
            const demoDaily = {
                list: Array(16).fill().map((_, i) => {
                    const date = new Date();
                    date.setDate(date.getDate() + i);
                    return {
                        dt: date.getTime() / 1000,
                        temp: { 
                            max: 25 + Math.floor(Math.random() * 8), 
                            min: 15 + Math.floor(Math.random() * 5) 
                        },
                        weather: [{ 
                            main: ['Clear', 'Clouds', 'Rain'][Math.floor(Math.random() * 3)], 
                            description: 'demo ma\'lumot' 
                        }]
                    };
                })
            };
            
            const demoSolar = {
                data: {
                    total: 5200,
                    average: 433,
                    max: 850
                }
            };
            
            displayAllWeather(demoCurrent, demoDaily, demoSolar, city);
        }

        function showLoading() {
            document.getElementById('content').innerHTML = `
                <div class="loading">
                    <div class="spinner"></div>
                    <p>Ma'lumotlar yuklanmoqda...</p>
                </div>
            `;
        }

        function showError(message) {
            document.getElementById('content').innerHTML = `
                <div class="error">
                    <div style="font-size: 48px; margin-bottom: 16px;">⚠️</div>
                    <h3>Xatolik yuz berdi</h3>
                    <p style="color: var(--text-secondary);">${message}</p>
                    <button class="btn" style="margin-top: 20px;" onclick="handleRegionChange()">Qayta urinish</button>
                </div>
            `;
        }

        // Dastlabki yuklash
        window.addEventListener('load', () => {
            setTimeout(() => {
                document.getElementById('regionSelect').value = 'Toshkent';
                handleRegionChange();
            }, 500);
        });
    </script>
</body>
</html>
