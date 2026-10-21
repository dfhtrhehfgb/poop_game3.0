<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>강아지의 똥 피하기 게임/title>
    <style>
        body {
            background-color: #151515;
            color: white;
            font-family: 'Arial', sans-serif;
            text-align: center;
            margin: 0;
            padding: 2px;
            user-select: none;
            -webkit-user-select: none;
            touch-action: manipulation;
        }
        h1 { font-size: 17px; margin: 3px 0; }
        h3 { margin: 3px 0; font-size: 12px; color: #ffcc00; }
        p { margin: 3px 0; font-size: 11px; }
        .screen { display: none; }
        .active { display: block; }
        
        .btn-container { margin: 4px 0; }
        button {
            padding: 6px 12px;
            font-size: 12px;
            margin: 3px;
            cursor: pointer;
            border: none;
            border-radius: 6px;
            font-weight: bold;
            display: inline-block;
            width: 75%;
            max-width: 200px;
            transition: transform 0.1s, background-color 0.2s;
        }
        button:hover { transform: scale(1.03); }
        .btn-easy { background-color: #4CAF50; color: white; }
        .btn-normal { background-color: #2196F3; color: white; }
        .btn-hard { background-color: #ff9800; color: white; }
        .btn-impossible { background-color: #d32f2f; color: white; }
        .btn-crazy { background-color: #8E44AD; color: white; box-shadow: 0 0 8px #8E44AD; }
        .btn-dict { background-color: #607d8b; color: white; }
        .btn-tutorial { background-color: #9c27b0; color: white; }
        .btn-quit { background-color: #555; color: white; padding: 3px 8px; font-size: 11px; width: auto; margin: 0; }
        .btn-del { background-color: #e91e63; color: white; padding: 1px 4px; font-size: 9px; width: auto; border-radius: 3px; cursor: pointer; }

        .game-layout-wrapper {
            display: flex;
            flex-direction: row;
            justify-content: center;
            align-items: flex-start;
            gap: 8px;
            max-width: 650px;
            margin: 0 auto;
        }

        .game-main-area {
            flex: 1;
            max-width: 420px;
        }

        .admin-sidebar {
            display: none;
            width: 160px;
            background: #222;
            border: 2px solid #ff4081;
            border-radius: 8px;
            padding: 6px;
            text-align: left;
            box-sizing: border-box;
            max-height: 360px;
            overflow-y: auto;
        }
        .admin-sidebar h4 {
            margin: 0 0 4px 0;
            font-size: 11px;
            color: #ff4081;
            text-align: center;
        }
        .admin-sidebar button {
            width: 100%;
            max-width: none;
            font-size: 10px;
            padding: 5px 3px;
            margin: 2px 0;
        }

        #game-board {
            width: 100%;
            max-width: 420px;
            height: 360px;
            background-color: #332211;
            margin: 0 auto;
            border-radius: 8px;
            position: relative;
            overflow: hidden;
            border: 3px solid #654321;
            box-sizing: border-box;
            touch-action: none; /* 모바일 드래그 시 화면 스크롤 방지 */
        }
        
        #blackout-overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.97);
            pointer-events: none;
            z-index: 6;
            display: none;
            mix-blend-mode: multiply;
        }
        
        .hud {
            display: flex;
            justify-content: space-between;
            align-items: center;
            width: 100%;
            max-width: 420px;
            margin: 0 auto;
            padding: 2px 0;
            font-size: 11px;
        }
        
        .player {
            position: absolute;
            bottom: 15px;
            width: 36px;
            height: 36px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            transition: background-color 0.2s;
            z-index: 7;
            background-color: #ffcc00;
        }
        
        .reversed { background-color: #ff00ff !important; box-shadow: 0 0 10px #ff00ff; }
        .slowed { background-color: #00bfff !important; box-shadow: 0 0 10px #00bfff; }
        .god-mode { border: 2px solid #ffeb3b; box-shadow: 0 0 8px #ffeb3b; }

        .poop {
            position: absolute;
            width: 26px;
            height: 26px;
            background-color: #8B4513;
            border-radius: 50% 50% 40% 40%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 14px;
            z-index: 5;
        }
        .poop-good { border: 2px solid #4CAF50; box-shadow: 0 0 5px #4CAF50; }
        .poop-bad { border: 2px solid #d32f2f; box-shadow: 0 0 5px #d32f2f; }

        #wave-banner {
            position: absolute;
            top: 40%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 18px;
            font-weight: bold;
            color: #ffeb3b;
            text-shadow: 0 0 8px #000, 0 0 15px #ff0000;
            display: none;
            z-index: 10;
            pointer-events: none;
            text-align: center;
            width: 90%;
        }

        #tutorial-guide-banner {
            position: absolute;
            top: 10px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.85);
            color: #00ffcc;
            padding: 6px 12px;
            border-radius: 16px;
            font-size: 11px;
            font-weight: bold;
            border: 2px solid #00ffcc;
            z-index: 15;
            display: none;
            text-align: center;
        }
        
        .rankings-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 4px;
            width: 100%;
            max-width: 420px;
            margin: 2px auto;
        }
        .ranking-box {
            background: #222;
            padding: 3px;
            border-radius: 5px;
        }
        .ranking-item {
            padding: 1px 0;
            border-bottom: 1px solid #333;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 9px;
            color: #aaa;
        }

        .dict-container {
            width: 92%;
            max-width: 400px;
            height: 260px;
            overflow-y: auto;
            margin: 3px auto;
            background: #222;
            padding: 6px;
            border-radius: 6px;
            text-align: left;
        }
        .dict-item {
            display: flex;
            align-items: flex-start;
            padding: 4px;
            border-bottom: 1px solid #333;
        }
        .dict-item.good-item {
            border-left: 3px solid #4CAF50;
            background-color: rgba(76, 175, 80, 0.08);
        }
        .dict-icon { font-size: 18px; width: 26px; text-align: center; flex-shrink: 0; }
        .dict-info { margin-left: 4px; width: 100%; }
        .dict-info h4 { margin: 0 0 1px 0; font-size: 11px; color: #ffcc00; }
        .dict-item.good-item .dict-info h4 { color: #81c784; }
        .dict-info p { margin: 0 0 1px 0; font-size: 10px; color: #ccc; }
        .dict-tip {
            margin: 1px 0 0 0;
            font-size: 9px;
            color: #ff9800;
            background: #151515;
            padding: 2px 4px;
            border-radius: 3px;
        }
        .dict-specs {
            font-size: 8px;
            color: #00ffff;
            background: #111;
            padding: 1px 3px;
            border-radius: 2px;
            display: inline-block;
            margin-bottom: 1px;
        }
        
        #admin-pin-modal, #admin-spawn-modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.8);
            justify-content: center;
            align-items: center;
            flex-direction: column;
            z-index: 20;
        }
        .modal-content {
            background: #222;
            padding: 14px;
            border-radius: 10px;
            border: 2px solid #555;
            width: 75%;
            max-width: 240px;
            text-align: center;
        }
        input[type="password"], input[type="text"] {
            padding: 5px;
            font-size: 12px;
            width: 80%;
            margin: 5px 0;
            border-radius: 5px;
            border: none;
            text-align: center;
            background: #333;
            color: white;
        }

        .bg-color-picker {
            margin: 2px 0;
            font-size: 10px;
        }
        .bg-color-picker select {
            padding: 2px;
            font-size: 10px;
            background: #222;
            color: white;
            border: 1px solid #444;
            border-radius: 3px;
        }
    </style>
</head>
<body>

    <div id="screen-menu" class="screen active">
        <h1 style="cursor: pointer;" onclick="handleDogEmojiClick()" title="관리자 잠금해제">🐶 강아지의 똥피하기 대작전</h1>
        <p style="color: #ff00ff; font-size: 10px; margin-bottom: 2px;">⚡ 모바일 터치 & 드래그 조작 지원!</p>
        
        <div class="bg-color-picker">
            🎨 배경 색상: 
            <select id="bg-color-select" onchange="changeBoardBackground(this.value)">
                <option value="#332211">갈색 흙바닥 (기본)</option>
                <option value="#ffeb3b" style="color: #000; font-weight: bold;">💛 찐노랑 배경</option>
                <option value="#00aa00" style="color: #fff; font-weight: bold;">💚 찐초록 배경</option>
                <option value="#ffffff" style="color: #000; font-weight: bold;">🤍 하양 배경</option>
                <option value="#112233">푸른 밤바다</option>
                <option value="#223311">초록 잔디밭</option>
                <option value="#331122">보라색 마법진</option>
                <option value="#111111">시크한 암흑</option>
                <option value="#443300" id="opt-admin-bg1" style="display:none;">👑 👑 [관리자] 황금빛 궁전</option>
                <option value="#002244" id="opt-admin-bg2" style="display:none;">🤖 [관리자] 홀로그램 네온</option>
            </select>
        </div>

        <div class="btn-container" style="margin: 2px 0;">
            <button class="btn-dict" onclick="openDict()" style="width: 47%; display: inline-block; margin: 1px;">📖 똥 도감</button>
            <button class="btn-tutorial" onclick="startTutorial()" style="width: 47%; display: inline-block; margin: 1px;">🎓 튜토리얼</button>
        </div>

        <div id="main-admin-exit-container" style="display: none; margin: 4px 0;">
            <button class="btn-impossible" onclick="adminExitMode()" style="width: 95%; max-width: 400px; padding: 5px; font-size: 11px; background-color: #d32f2f;">🚪 [관리자] 메인 화면에서 관리자 모드 나가기</button>
        </div>

        <h3 style="color: #4CAF50; margin: 1px 0;">🟢 초급자</h3>
        <div class="btn-container" style="margin-top: 0;">
            <button class="btn-easy" onclick="showDifficultyMenu('초급자')">초급자 모드 선택</button>
        </div>

        <h3 style="color: #2196F3; margin: 1px 0;">🔵 중급자</h3>
        <div class="btn-container" style="margin-top: 0;">
            <button class="btn-normal" onclick="showDifficultyMenu('중급자')">중급자 모드 선택</button>
        </div>

        <h3 style="color: #ff9800; margin: 1px 0;">🟠 고급자</h3>
        <div class="btn-container" style="margin-top: 0;">
            <button class="btn-hard" onclick="showDifficultyMenu('고급자')">고급자 모드 선택</button>
        </div>

        <h3 style="color: #d32f2f; margin: 1px 0;">💀 불가능</h3>
        <div class="btn-container" style="margin-top: 0;">
            <button class="btn-impossible" onclick="showDifficultyMenu('불가능')">불가능 모드 선택</button>
        </div>

        <h3 style="color: #8E44AD; margin: 1px 0;">⚡ 크레이지 (지옥 난이도)</h3>
        <div class="btn-container" style="margin-top: 0;">
            <button class="btn-crazy" onclick="showDifficultyMenu('크레이지')">크레이지 모드 선택</button>
        </div>

        <h3 style="margin: 2px 0; font-size: 10px;">🏆 랭킹</h3>
        <div class="rankings-grid">
            <div class="ranking-box">
                <h4 style="margin: 1px 0; font-size: 9px; color: #4CAF50;">🟢 초급자</h4>
                <div id="ranking-list-초급자"></div>
            </div>
            <div class="ranking-box">
                <h4 style="margin: 1px 0; font-size: 9px; color: #2196F3;">🔵 중급자</h4>
                <div id="ranking-list-중급자"></div>
            </div>
        </div>
        <div style="margin-top: 1px;">
            <div class="rankings-grid" style="margin: 0 auto;">
                <div class="ranking-box">
                    <h4 style="margin: 1px 0; font-size: 9px; color: #ff9800;">🟠 고급자</h4>
                    <div id="ranking-list-고급자"></div>
                </div>
                <div class="ranking-box">
                    <h4 style="margin: 1px 0; font-size: 9px; color: #d32f2f;">💀 불가능</h4>
                    <div id="ranking-list-불가능"></div>
                </div>
            </div>
        </div>
        <div style="margin-top: 1px;">
            <div class="ranking-box" style="width: 100%; max-width: 420px; margin: 0 auto;">
                <h4 style="margin: 1px 0; font-size: 9px; color: #8E44AD;">⚡ 크레이지</h4>
                <div id="ranking-list-크레이지"></div>
            </div>
        </div>
    </div>

    <div id="screen-difficulty" class="screen">
        <h1 id="diff-title-text">세부 난이도 선택</h1>
        <p style="font-size: 10px; color: #aaa; margin: 2px 0;">서브 난이도 선택</p>

        <div class="btn-container">
            <button class="btn-easy" onclick="startGameFromSub('쉬움')">🟢 쉬움</button><br>
            <button class="btn-normal" onclick="startGameFromSub('보통')">🔵 보통</button><br>
            <button class="btn-hard" onclick="startGameFromSub('어려움')">🟠 어려움</button><br>
            <button class="btn-quit" onclick="returnToMenu()" style="width: 75%; max-width: 200px; padding: 5px; font-size: 11px; margin-top: 3px;">뒤로 가기</button>
        </div>
    </div>

    <div id="screen-dict" class="screen">
        <h1>📖 똥 도감</h1>
        <p style="font-size: 10px; color: #81c784; margin: 2px 0;">🟩 초록색: 좋은 아이템 | 🟥 빨간색: 피해야 할 똥</p>
        <div class="dict-container" id="dict-list-container"></div>
        <button class="btn-normal" onclick="returnToMenu()" style="width: auto; padding: 4px 12px; margin-top: 3px;">메인으로</button>
    </div>

    <div id="screen-game" class="screen">
        <div class="hud" id="hud-container">
            <span id="ui-lives">❤️❤️❤️❤️❤️</span>
            <span id="ui-wave" style="color: #ffeb3b; font-weight: bold;">웨이브 1</span>
            <span id="ui-score">점수: 0</span>
            <div>
                <button class="btn-quit" onclick="quitGame()">나가기</button>
            </div>
        </div>
        
        <div class="game-layout-wrapper">
            <div class="game-main-area">
                <div id="game-board">
                    <div id="blackout-overlay"></div>
                    <div id="wave-banner">WAVE 1</div>
                    <div id="tutorial-guide-banner">튜토리얼 진행 중</div>
                    <div id="player1" class="player" style="left: calc(50% - 18px);">🐶</div>
                </div>
                <p style="color: #aaa; font-size: 10px;" id="control-guide">화면을 터치한 채 좌우로 드래그하여 이동하세요!</p>
            </div>

            <div id="admin-sidebar" class="admin-sidebar">
                <h4>🛠️ 슈퍼 관리자</h4>
                <button class="btn-easy" onclick="adminToggleGodMode()" id="admin-god-btn">1. 무적: 켜기</button>
                <button class="btn-normal" onclick="adminFullHeal()">2. 풀회복</button>
                <button class="btn-hard" onclick="adminAddScore()">3. 점수+500</button>
                <button class="btn-impossible" onclick="adminClearPoops()">4. 똥 클리어</button>
                <button class="btn-tutorial" onclick="adminToggleFreeze()">5. 시간 멈춤</button>
                <button class="btn-dict" onclick="adminShieldPush()">6. 똥 밀어내기</button>
                <button class="btn-easy" onclick="adminSkipWave()" style="background-color: #673AB7; color: white;">7. 웨이브 스킵</button>
                <button class="btn-hard" onclick="openAdminSpawnModal()" style="background-color: #ff5722; color: white;">8. 원하는 똥 소환</button>
            </div>
        </div>
    </div>

    <div id="screen-gameover" class="screen">
        <h1 id="gameover-title">💩 게임 오버!</h1>
        <p id="killer-info" style="font-size: 12px; color: #ff6b6b; margin: 3px 0; font-weight: bold;"></p>
        <p id="final-score" style="font-size: 13px; font-weight: bold; margin: 3px 0;"></p>
        <p id="cheer-msg" style="color: #ffeb3b; font-size: 11px; font-weight: bold; margin: 4px 0;"></p>
        
        <div id="ranking-prompt-area" style="margin: 6px 0; background: #222; padding: 6px; border-radius: 6px; border: 1px solid #444;">
            <p style="font-size: 11px; color: #ffcc00; margin-bottom: 3px;">🏆 랭킹에 기록하시겠습니까?</p>
            <input type="text" id="ranking-name-input" placeholder="플레이어 이름 입력 (예: 댕댕이)" maxlength="10">
            <br>
            <button class="btn-easy" onclick="submitRankingYes()" style="width: 45%; padding: 4px; font-size: 11px; margin: 2px;">등록하기</button>
            <button class="btn-quit" onclick="submitRankingNo()" style="width: 45%; padding: 4px; font-size: 11px; margin: 2px;">건너뛰기</button>
        </div>
        <p id="ranking-status-msg" style="color: #4CAF50; font-size: 10px; margin-top: 4px; display: none;"></p>

        <br>
        <button class="btn-normal" onclick="returnToMenu()" style="width: auto; margin-top: 2px;">메인으로 돌아가기</button>
    </div>

    <div id="admin-pin-modal">
        <div class="modal-content">
            <h3 style="margin-top: 0; color: #ff4081;">🔒 관리자 PIN 입력</h3>
            <p style="font-size: 10px; color: #aaa;">4자리 PIN 번호를 입력하세요</p>
            <input type="password" id="admin-pin-input" placeholder="PIN" maxlength="4">
            <br>
            <button class="btn-easy" onclick="checkAdminPin()" style="width: 100%; padding: 5px; margin: 2px 0;">확인</button>
            <button class="btn-quit" onclick="closeAdminPinModal()" style="width: 100%; padding: 5px; margin: 2px 0;">취소</button>
        </div>
    </div>

    <div id="admin-spawn-modal">
        <div class="modal-content" style="width: 90%; max-width: 300px; max-height: 80vh; overflow-y: auto;">
            <h3 style="margin-top: 0; color: #ff5722;">🎯 원하는 똥 소환</h3>
            <p style="font-size: 10px; color: #aaa;">소환할 똥을 선택하세요</p>
            <div id="admin-spawn-list" style="display: flex; flex-direction: column; gap: 4px; margin: 6px 0;"></div>
            <button class="btn-quit" onclick="closeAdminSpawnModal()" style="width: 100%; padding: 5px; margin-top: 4px;">닫기</button>
        </div>
    </div>

<script>
    let dogClickCount = 0;
    let dogClickTimer = null;

    function handleDogEmojiClick() {
        dogClickCount++;
        if (dogClickTimer) clearTimeout(dogClickTimer);
        
        if (dogClickCount >= 5) {
            dogClickCount = 0;
            document.getElementById('admin-pin-modal').style.display = 'flex';
            document.getElementById('admin-pin-input').value = '';
            document.getElementById('admin-pin-input').focus();
        } else {
            dogClickTimer = setTimeout(() => { dogClickCount = 0; }, 1000);
        }
    }

    let isAdminLoggedIn = false;
    let isGodMode = false;
    let isTimeFrozen = false;
    let adminCharacterIcon = '🐶';

    function checkAdminPin() {
        let pinVal = document.getElementById('admin-pin-input').value;
        if (pinVal === '0419') {
            document.getElementById('admin-pin-modal').style.display = 'none';
            alert('🔓 관리자 인증 성공! 랭킹 등록이 완전히 차단되며 관리자 능력이 활성화됩니다.');
            isAdminLoggedIn = true;
            
            document.getElementById('opt-admin-bg1').style.display = 'block';
            document.getElementById('opt-admin-bg2').style.display = 'block';
            document.getElementById('main-admin-exit-container').style.display = 'block';

            const adminAvatars = ['🦁', '🦄', '🤖', '👑'];
            adminCharacterIcon = adminAvatars[Math.floor(Math.random() * adminAvatars.length)];
            document.getElementById('player1').innerText = adminCharacterIcon;

            if (document.getElementById('screen-game').classList.contains('active')) {
                document.getElementById('admin-sidebar').style.display = 'block';
            }
            displayRankings();
        } else {
            alert('❌ PIN 번호가 틀렸습니다!');
            document.getElementById('admin-pin-input').value = '';
        }
    }

    function closeAdminPinModal() {
        document.getElementById('admin-pin-modal').style.display = 'none';
        dogClickCount = 0;
    }

    function adminExitMode() {
        if (!confirm("정말 관리자 모드를 나가시겠습니까?")) return;
        isAdminLoggedIn = false;
        isGodMode = false;
        document.getElementById('player1').innerText = '🐶';
        document.getElementById('player1').classList.remove('god-mode');
        document.getElementById('admin-sidebar').style.display = 'none';
        document.getElementById('opt-admin-bg1').style.display = 'none';
        document.getElementById('opt-admin-bg2').style.display = 'none';
        document.getElementById('main-admin-exit-container').style.display = 'none';
        alert('🚪 관리자 모드가 해제되었습니다.');
        displayRankings();
    }

    function adminToggleGodMode() {
        if (!isAdminLoggedIn) return;
        isGodMode = !isGodMode;
        let pElem = document.getElementById('player1');
        if (isGodMode) pElem.classList.add('god-mode');
        else pElem.classList.remove('god-mode');
        document.getElementById('admin-god-btn').innerText = `1. 무적: ${isGodMode ? '끄기' : '켜기'}`;
        updateHUD();
    }

    function adminFullHeal() {
        if (!isAdminLoggedIn) return;
        p1Lives = 5;
        updateHUD();
    }

    function adminAddScore() {
        if (!isAdminLoggedIn) return;
        score += 500;
        updateHUD();
    }

    function adminClearPoops() {
        if (!isAdminLoggedIn) return;
        poops.forEach(p => { if (p && p.element) p.element.remove(); });
        poops = [];
    }

    function adminToggleFreeze() {
        if (!isAdminLoggedIn) return;
        isTimeFrozen = !isTimeFrozen;
        alert(isTimeFrozen ? '⏱️ 시간이 멈췄습니다!' : '⏱️ 시간이 다시 흘러갑니다!');
    }

    function adminShieldPush() {
        if (!isAdminLoggedIn) return;
        poops.forEach(p => {
            if (p && p.y < 250) p.y -= 80;
        });
        showWaveBanner('🛡️ 관리자 배리어 발동!');
    }

    function adminSkipWave() {
        if (!isAdminLoggedIn) return;
        currentWave++;
        waveProgressTime = 0;
        updateHUD();
        showWaveBanner(`⚡ 관리자 웨이브 스킵! WAVE ${currentWave}`);
    }

    function openAdminSpawnModal() {
        if (!isAdminLoggedIn) return;
        let spawnListContainer = document.getElementById('admin-spawn-list');
        spawnListContainer.innerHTML = '';
        poopDictionary.forEach(item => {
            let btn = document.createElement('button');
            btn.classList.add('btn-normal');
            btn.style.width = '100%';
            btn.style.maxWidth = 'none';
            btn.style.fontSize = '11px';
            btn.style.padding = '5px';
            btn.innerText = `${item.icon} ${item.name}`;
            btn.onclick = () => {
                spawnSpecificPoop(item);
                closeAdminSpawnModal();
            };
            spawnListContainer.appendChild(btn);
        });
        document.getElementById('admin-spawn-modal').style.display = 'flex';
    }

    function closeAdminSpawnModal() {
        document.getElementById('admin-spawn-modal').style.display = 'none';
    }

    function spawnSpecificPoop(chosen) {
        let poopElem = document.createElement('div');
        poopElem.classList.add('poop');
        if (chosen.isGood) poopElem.classList.add('poop-good');
        else poopElem.classList.add('poop-bad');
        poopElem.innerText = chosen.icon;

        let widthVal = chosen.widthVal || 26;
        poopElem.style.width = widthVal + 'px';
        poopElem.style.height = widthVal + 'px';
        poopElem.style.fontSize = (widthVal * 0.54) + 'px';

        let startX = Math.random() * (394 - widthVal);
        poopElem.style.left = startX + 'px';
        poopElem.style.top = '-120px';
        document.getElementById('game-board').appendChild(poopElem);

        poops.push({
            element: poopElem,
            x: startX,
            y: -120,
            width: widthVal,
            speed: chosen.type === 'fast' ? 2.5 : 1.2,
            type: chosen.type,
            isGood: chosen.isGood,
            name: chosen.name,
            icon: chosen.icon,
            dir: Math.random() < 0.5 ? 1 : -1,
            exploded: false,
            splitDone: false,
            multiSplitDone: false,
            secondExploded: false
        });
        showWaveBanner(`🎯 ${chosen.name} 소환됨!`);
    }

    const poopDictionary = [
        { icon: '💩', name: '기본 똥', desc: '가장 흔하고 평범한 똥입니다.', size: '보통 (26px)', speed: '보통 상향', type: 'normal', movement: 'straight', isGood: false, tier: '초급자', tip: '정직한 궤도이므로 빈 공간을 살짝 비켜서 피하세요.', widthVal: 26 },
        { icon: '💖', name: '회복 하트', desc: '체력을 1칸 회복해 줍니다.', size: '작음 (22px)', speed: '상향됨', type: 'heal', movement: 'straight', isGood: true, tier: '초급자', tip: '체력이 부족할 때 최우선으로 챙기세요.', widthVal: 22 },
        { icon: '🌈', name: '무지개 똥', desc: '추가 점수를 줍니다!', size: '작음 (22px)', speed: '상향됨', type: 'rainbow', movement: 'straight', isGood: true, tier: '초급자', tip: '안전하다면 무조건 획득하세요.', widthVal: 22 },
        { icon: '🌀', name: '회오리 똥', desc: '지그재그 곡선으로 낙하합니다.', size: '보통 (26px)', speed: '상향됨', type: 'zigzag', movement: 'zigzag', isGood: false, tier: '중급자', tip: '좌우 움직임을 보고 반대로 피하세요.', widthVal: 26 },
        { icon: '⚡', name: '번개 똥', desc: '초고속으로 낙하합니다!', size: '보통 (26px)', speed: '초고속 (2.5)', type: 'fast', movement: 'straight', isGood: false, tier: '중급자', tip: '빛보다 빠르게 떨어지니 주의하세요.', widthVal: 26 },
        { icon: '🔄', name: '반전 똥', desc: '좌우 이동 키를 뒤집습니다.', size: '보통 (26px)', speed: '상향됨', type: 'reverse', movement: 'straight', isGood: false, tier: '중급자', tip: '반대 키를 누르세요.', widthVal: 26 },
        { icon: '❄️', name: '얼음 똥', desc: '5초 동안 이동 속도를 느리게 만듭니다.', size: '보통 (26px)', speed: '상향됨', type: 'ice', movement: 'straight', isGood: false, tier: '중급자', tip: '무리한 움직임을 피하세요.', widthVal: 26 },
        { icon: '💥', name: '폭발 똥', desc: '상단에서 곧바로 터지며 파편을 흩뿌립니다!', size: '보통 (26px)', speed: '상향됨', type: 'explosive', movement: 'explosive', isGood: false, tier: '고급자', tip: '상단 폭발 시 튀어나오는 파편 궤도를 예측하세요.', widthVal: 26 },
        { icon: '☢️', name: '돌연변이 폭발 똥', desc: '상단에서 파편이 솟구쳤다 2차 분열합니다.', size: '보통 (26px)', speed: '상향됨', type: 'mutant_explosive', movement: 'mutant_explosive', isGood: false, tier: '불가능', tip: '파편의 확산 범위가 넓어 예측이 필요합니다.', widthVal: 26 },
        { icon: '🦠', name: '분열 똥', desc: '중간 지점에서 두 갈래로 나뉩니다!', size: '보통 (26px)', speed: '상향됨', type: 'split', movement: 'split', isGood: false, tier: '불가능', tip: '분열할 때 옆 라인으로 빠르게 피하세요.', widthVal: 26 },
        { icon: '🧬', name: '돌연변이 분열 똥', desc: '중간 지점에서 사방으로 연속 분열합니다!', size: '보통 (26px)', speed: '상향됨', type: 'multi_split', movement: 'multi_split', isGood: false, tier: '불가능', tip: '연속 분열 궤도를 피해 구석으로 대피하세요.', widthVal: 26 },
        { icon: '💫', name: '양방향 커브 똥', desc: '벽에 닿으면 퉁기는 대각선 커브 똥입니다.', size: '보통 (26px)', speed: '상향됨', type: 'curve', movement: 'curve', isGood: false, tier: '고급자', tip: '벽 반사각을 예측하여 피하세요.', widthVal: 26 },
        { icon: '💣', name: '폭탄 똥', desc: '체력을 2칸 깎습니다!', size: '보통 (26px)', speed: '상향됨', type: 'bomb', movement: 'straight', isGood: false, tier: '고급자', tip: '최우선으로 피하세요.', widthVal: 26 },
        { icon: '🌑', name: '일식 똥', desc: '화면 전체를 암흑으로 물들입니다.', size: '보통 (26px)', speed: '상향됨', type: 'blackout', movement: 'straight', isGood: false, tier: '불가능', tip: '시야가 차단된 동안에는 침착하게 이동하세요.', widthVal: 26 },
        { icon: '💀', name: '지옥의 해골 폭탄 똥', desc: '스치는 순간 체력을 무려 3칸이나 앗아가는 최악의 해골 똥입니다.', size: '특대 (30px)', speed: '극악 (2.8)', type: 'skull_bomb', movement: 'straight', isGood: false, tier: '크레이지', tip: '크레이지 난이도의 주범입니다. 무조건 피하세요.', widthVal: 30 },
        { icon: '🕳️', name: '블랙홀 싱크홀 똥', desc: '강아지를 중앙으로 강하게 끌어당기는 중력 인력장을 만듭니다.', size: '보통 (26px)', speed: '빠름 (2.2)', type: 'sinkhole', movement: 'straight', isGood: false, tier: '크레이지', tip: '끌려가는 힘을 계산해 반대 방향으로 버티세요.', widthVal: 26 }
    ];

    function initDictUI() {
        let container = document.getElementById('dict-list-container');
        container.innerHTML = '';
        poopDictionary.forEach((item, index) => {
            let div = document.createElement('div');
            div.classList.add('dict-item');
            if (item.isGood) div.classList.add('good-item');
            div.innerHTML = `
                <div class="dict-icon">${item.icon}</div>
                <div class="dict-info">
                    <h4>${index + 1}. ${item.name} [${item.tier}] ${item.isGood ? '✨' : ''}</h4>
                    <p>${item.desc}</p>
                    <div class="dict-specs">크기: ${item.size} | 속도: ${item.speed}</div>
                    <div class="dict-tip">💡 공략: ${item.tip}</div>
                </div>
            `;
            container.appendChild(div);
        });
    }
    initDictUI();

    function changeBoardBackground(color) {
        let board = document.getElementById('game-board');
        board.style.backgroundColor = color;
        if (color === '#ffffff' || color === '#ffeb3b') {
            board.style.borderColor = '#444';
        } else {
            board.style.borderColor = color === '#111111' ? '#333' : '#654321';
        }
    }

    let selectedTier = '초급자';
    let selectedSubDiff = '보통';
    let isTutorialMode = false;
    let tutorialStep = 0;

    function showDifficultyMenu(tier) {
        selectedTier = tier;
        isTutorialMode = false;
        document.getElementById('diff-title-text').innerText = `${tier} - 서브 난이도 선택`;
        hideAllScreens();
        document.getElementById('screen-difficulty').classList.add('active');
    }

    function openDict() {
        hideAllScreens();
        document.getElementById('screen-dict').classList.add('active');
    }

    function clearAllTimers() {
        if (p1Timers.rev) clearTimeout(p1Timers.rev);
        if (p1Timers.slow) clearTimeout(p1Timers.slow);
        if (p1Timers.blackout) clearTimeout(p1Timers.blackout);
    }

    function hideAllScreens() {
        const screens = document.querySelectorAll('.screen');
        screens.forEach(s => s.classList.remove('active'));
    }

    function returnToMenu() {
        stopBGM();
        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        clearAllTimers();
        poops.forEach(p => { if (p && p.element) p.element.remove(); });
        poops = [];
        document.getElementById('tutorial-guide-banner').style.display = 'none';
        document.getElementById('blackout-overlay').style.display = 'none';
        document.getElementById('admin-pin-modal').style.display = 'none';
        document.getElementById('admin-spawn-modal').style.display = 'none';

        hideAllScreens();
        document.getElementById('screen-menu').classList.add('active');
        displayRankings();
    }

    let audioCtx = null;
    let bgmTimer = null;
    let bgmStep = 0;

    function initAudio() {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        if (audioCtx.state === 'suspended') audioCtx.resume();
    }

    function playHitSound() {
        if (!audioCtx) return;
        let osc = audioCtx.createOscillator();
        let gain = audioCtx.createGain();
        osc.type = 'sawtooth';
        osc.frequency.setValueAtTime(150, audioCtx.currentTime);
        osc.frequency.exponentialRampToValueAtTime(40, audioCtx.currentTime + 0.12);
        gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
        gain.gain.linearRampToValueAtTime(0.01, audioCtx.currentTime + 0.12);
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.12);
    }

    function playItemSound(isGood) {
        if (!audioCtx) return;
        let osc = audioCtx.createOscillator();
        let gain = audioCtx.createGain();
        osc.type = 'sine';
        if (isGood) {
            osc.frequency.setValueAtTime(300, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(600, audioCtx.currentTime + 0.12);
        } else {
            osc.frequency.setValueAtTime(200, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(80, audioCtx.currentTime + 0.12);
        }
        gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
        gain.gain.linearRampToValueAtTime(0.01, audioCtx.currentTime + 0.12);
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.12);
    }

    function startBGM() {
        stopBGM();
        bgmStep = 0;
        playBGMStep();
    }

    function playBGMStep() {
        if (!document.getElementById('screen-game').classList.contains('active')) return;
        if (audioCtx) {
            let osc = audioCtx.createOscillator();
            let gain = audioCtx.createGain();
            const notes = [261.63, 293.66, 329.63, 349.23, 392.00, 440.00];
            osc.frequency.value = notes[bgmStep % notes.length];
            osc.type = 'triangle';
            gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.1);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start();
            osc.stop(audioCtx.currentTime + 0.1);
            bgmStep++;
        }
        bgmTimer = setTimeout(playBGMStep, 350);
    }

    function stopBGM() {
        if (bgmTimer) { clearTimeout(bgmTimer); bgmTimer = null; }
    }

    let score = 0;
    let scoreAcc = 0;
    let p1Lives = 5;
    let poops = [];
    let gameLoopId = null;
    let p1X = 190;
    let lastKiller = { name: '기본 똥', icon: '💩' };
    
    let spawnTimer = 0;
    let baseSpawnRate = 2000;
    let currentSpawnRate = 2000;
    let currentWave = 1;
    let waveProgressTime = 0;

    let p1Reversed = false, p1Slowed = false, isBlackoutActive = false;
    let p1Timers = { rev: null, slow: null, blackout: null };
    let p1Left = false, p1Str = false;

    // 터치 조작 관련 변수
    let isTouching = false;
    let touchTargetX = 190;

    function getRankings(tier) {
        let saved = localStorage.getItem(`poop_rankings_${tier}_v75`);
        return saved ? JSON.parse(saved) : { '쉬움': [], '보통': [], '어려움': [] };
    }

    function saveRankings(tier, rankData) {
        localStorage.setItem(`poop_rankings_${tier}_v75`, JSON.stringify(rankData));
    }

    function deleteRankingItem(tier, sub, index) {
        if (!isAdminLoggedIn) return;
        if (confirm(`[${tier} - ${sub}] 랭킹 ${index+1}등 기록을 삭제하시겠습니까?`)) {
            let rankData = getRankings(tier);
            if (rankData[sub] && rankData[sub][index]) {
                rankData[sub].splice(index, 1);
                saveRankings(tier, rankData);
                displayRankings();
            }
        }
    }

    function displayRankings() {
        const tiers = ['초급자', '중급자', '고급자', '불가능', '크레이지'];
        tiers.forEach(tier => {
            let container = document.getElementById(`ranking-list-${tier}`);
            if (!container) return;
            let rankData = getRankings(tier);
            container.innerHTML = '';
            let subs = ['쉬움', '보통', '어려움'];
            subs.forEach(sub => {
                let list = rankData[sub] || [];
                if (list.length > 0) {
                    let subHeader = document.createElement('div');
                    subHeader.style.fontSize = '8px';
                    subHeader.style.color = '#ffcc00';
                    subHeader.style.margin = '1px 0';
                    subHeader.innerText = `[${sub}]`;
                    container.appendChild(subHeader);

                    list.slice(0, 3).forEach((item, idx) => {
                        let itemDiv = document.createElement('div');
                        itemDiv.classList.add('ranking-item');
                        let playerName = item.name ? item.name : '익명';
                        let delBtnHTML = isAdminLoggedIn ? `<button class="btn-del" onclick="deleteRankingItem('${tier}', '${sub}', ${idx})">삭제</button>` : '';
                        itemDiv.innerHTML = `<span>${idx+1}. ${playerName} (${item.score}점)</span> ${delBtnHTML}`;
                        container.appendChild(itemDiv);
                    });
                }
            });
            if (container.innerHTML === '') {
                container.innerHTML = '<div style="font-size: 8px; color: #666; padding: 2px;">기록 없음</div>';
            }
        });
    }
    displayRankings();

    function startGameFromSub(sub) {
        selectedSubDiff = sub;
        isTutorialMode = false;
        hideAllScreens();
        document.getElementById('screen-game').classList.add('active');
        document.getElementById('admin-sidebar').style.display = isAdminLoggedIn ? 'block' : 'none';
        document.getElementById('tutorial-guide-banner').style.display = 'none';
        initGameSettings();
    }

    function startTutorial() {
        selectedTier = '초급자';
        selectedSubDiff = '쉬움';
        isTutorialMode = true;
        tutorialStep = 0;
        hideAllScreens();
        document.getElementById('screen-game').classList.add('active');
        document.getElementById('admin-sidebar').style.display = 'none';
        document.getElementById('tutorial-guide-banner').style.display = 'block';
        document.getElementById('tutorial-guide-banner').innerText = '🎓 튜토리얼: 화면을 터치/드래그하여 똥을 피해보세요!';
        initGameSettings();
    }

    function initGameSettings() {
        initAudio();
        score = 0;
        scoreAcc = 0;
        p1Lives = 5;
        currentWave = 1;
        waveProgressTime = 0;
        clearAllTimers();
        p1Reversed = false;
        p1Slowed = false;
        isBlackoutActive = false;
        let pElem = document.getElementById('player1');
        pElem.classList.remove('slowed', 'reversed');
        poops.forEach(p => { if (p && p.element) p.element.remove(); });
        poops = [];
        p1X = 184;
        touchTargetX = p1X;
        pElem.style.left = p1X + 'px';
        isGodMode = false;
        isTimeFrozen = false;
        pElem.classList.remove('god-mode');
        document.getElementById('admin-god-btn').innerText = '1. 무적: 켜기';
        document.getElementById('blackout-overlay').style.display = 'none';

        if (selectedTier === '초급자') baseSpawnRate = 1800;
        else if (selectedTier === '중급자') baseSpawnRate = 1400;
        else if (selectedTier === '고급자') baseSpawnRate = 1100;
        else if (selectedTier === '불가능') baseSpawnRate = 850;
        else baseSpawnRate = 550;

        if (selectedSubDiff === '쉬움') baseSpawnRate *= 1.2;
        else if (selectedSubDiff === '어려움') baseSpawnRate *= 0.65;

        currentSpawnRate = baseSpawnRate;
        spawnTimer = 0;

        updateHUD();
        startBGM();
        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        lastTime = performance.now();
        gameLoopId = requestAnimationFrame(gameLoop);
    }

    function updateHUD() {
        let hearts = '';
        for (let i = 0; i < Math.max(0, p1Lives); i++) hearts += '❤️';
        document.getElementById('ui-lives').innerText = isGodMode ? '👑 무적 모드' : hearts;
        document.getElementById('ui-wave').innerText = `웨이브 ${currentWave}`;
        document.getElementById('ui-score').innerText = `점수: ${Math.floor(score)}`;
    }

    function quitGame() {
        returnToMenu();
    }

    let lastTime = 0;

    function gameLoop(timestamp) {
        if (!lastTime) lastTime = timestamp;
        let dt = timestamp - lastTime;
        lastTime = timestamp;

        if (dt > 100) dt = 100;

        if (!isTimeFrozen) {
            let moveSpeed = 6.0;
            if (p1Slowed) moveSpeed = 3.2;

            let sinkholePull = 0;
            poops.forEach(p => {
                if (p.type === 'sinkhole' && p.y > 50 && p.y < 300) {
                    let boardCenter = 197;
                    if (p1X < boardCenter) sinkholePull = 0.8;
                    else if (p1X > boardCenter) sinkholePull = -0.8;
                }
            });

            // 터치 조작 우선 반영 (터치 중이면 터치한 위치로 부드럽게 이동)
            if (isTouching) {
                let diff = touchTargetX - (p1X + 18); // 강아지 중심 기준
                let stepVal = diff * 0.25;
                if (p1Reversed) stepVal *= -1;
                p1X += stepVal + sinkholePull;
            } else {
                if (p1Left) {
                    p1X += (p1Reversed ? moveSpeed : -moveSpeed) + sinkholePull;
                } else if (p1Str) {
                    p1X += (p1Reversed ? -moveSpeed : moveSpeed) + sinkholePull;
                } else if (sinkholePull !== 0) {
                    p1X += sinkholePull * 1.5;
                }
            }

            p1X = Math.max(0, Math.min(384, p1X));
            document.getElementById('player1').style.left = p1X + 'px';

            scoreAcc += dt * (selectedTier === '크레이지' ? 0.12 : 0.06);
            if (scoreAcc >= 1) {
                score += Math.floor(scoreAcc);
                scoreAcc %= 1;
            }

            waveProgressTime += dt;
            if (waveProgressTime >= 14000 && !isTutorialMode) {
                waveProgressTime = 0;
                currentWave++;
                showWaveBanner(`WAVE ${currentWave}`);
            }

            spawnTimer += dt;
            if (spawnTimer >= currentSpawnRate) {
                spawnTimer = 0;
                spawnPoopItem();
            }

            updatePoops(dt);
        }

        if (document.getElementById('screen-game').classList.contains('active')) {
            gameLoopId = requestAnimationFrame(gameLoop);
        }
    }

    function spawnPoopItem() {
        let availablePoops = poopDictionary.filter(p => {
            if (isTutorialMode) return p.type === 'normal' || p.type === 'heal';
            if (selectedTier === '초급자') return p.tier === '초급자';
            if (selectedTier === '중급자') return p.tier === '초급자' || p.tier === '중급자';
            if (selectedTier === '고급자') return p.tier !== '불가능' && p.tier !== '크레이지';
            if (selectedTier === '불가능') return p.tier !== '크레이지';
            return true;
        });

        if (availablePoops.length === 0) availablePoops = [poopDictionary[0]];

        let chosen = availablePoops[Math.floor(Math.random() * availablePoops.length)];
        let poopElem = document.createElement('div');
        poopElem.classList.add('poop');
        if (chosen.isGood) poopElem.classList.add('poop-good');
        else poopElem.classList.add('poop-bad');
        poopElem.innerText = chosen.icon;

        let widthVal = chosen.widthVal || 26;
        poopElem.style.width = widthVal + 'px';
        poopElem.style.height = widthVal + 'px';
        poopElem.style.fontSize = (widthVal * 0.54) + 'px';

        let startX = Math.random() * (394 - widthVal);
        poopElem.style.left = startX + 'px';
        poopElem.style.top = '-120px';
        document.getElementById('game-board').appendChild(poopElem);

        let speedMult = 0.6 + Math.random() * 0.35;
        if (selectedTier === '중급자') speedMult = 0.7 + Math.random() * 0.3;
        if (selectedTier === '고급자') speedMult = 0.85 + Math.random() * 0.3;
        if (selectedTier === '불가능') speedMult = 1.1 + Math.random() * 0.3;
        if (selectedTier === '크레이지') speedMult = 1.45 + Math.random() * 0.45;

        let finalSpeed = (1.1 + Math.random() * 0.5) * speedMult;
        if (chosen.type === 'fast') finalSpeed = 2.8;
        if (chosen.type === 'skull_bomb') finalSpeed = 2.6;

        poops.push({
            element: poopElem,
            x: startX,
            y: -120,
            width: widthVal,
            speed: finalSpeed,
            type: chosen.type,
            isGood: chosen.isGood,
            name: chosen.name,
            icon: chosen.icon,
            dir: Math.random() < 0.5 ? 1 : -1,
            exploded: false,
            splitDone: false,
            multiSplitDone: false,
            secondExploded: false
        });
    }

    function updatePoops(dt) {
        let boardHeight = 360;
        for (let i = poops.length - 1; i >= 0; i--) {
            let p = poops[i];
            
            if (p.vy !== undefined) {
                p.vy += 0.09;
                p.y += p.vy * (dt * 0.06);
            } else {
                p.y += p.speed * (dt * 0.06);
            }

            if (p.vx !== undefined) {
                p.x += p.vx;
            }

            if (p.type === 'zigzag') {
                p.x += Math.cos(p.y * 0.03) * 1.8;
            } else if (p.type === 'curve') {
                p.x += p.dir * 1.3;
                if (p.x <= 0 || p.x >= (394 - p.width)) p.dir *= -1;
            } else if (p.type === 'split' && p.y > 120 && !p.splitDone) {
                p.splitDone = true;
                spawnSplitFragments(p.x, p.y);
            } else if (p.type === 'multi_split' && p.y > 120 && !p.multiSplitDone) {
                p.multiSplitDone = true;
                spawnMultiSplitFragments(p.x, p.y);
            } else if (p.type === 'explosive' && p.y >= 35 && !p.exploded) {
                p.exploded = true;
                spawnExplosiveFragments(p.x, p.y);
                p.element.remove();
                poops.splice(i, 1);
                continue;
            } else if (p.type === 'mutant_explosive' && p.y >= 35 && !p.exploded) {
                p.exploded = true;
                spawnMutantFragments(p.x, p.y);
                p.element.remove();
                poops.splice(i, 1);
                continue;
            } else if (p.type === 'mutant_fragment') {
                if (p.vy > 0 && !p.splitDone && p.y >= 70) {
                    p.splitDone = true;
                    spawnSecondMutantFragments(p.x, p.y);
                }
            }

            if (p.vx !== undefined && p.type !== 'shrapnel' && p.type !== 'mutant_fragment' && p.type !== 'second_fragment') {
                if (p.x <= 0 || p.x >= (394 - p.width)) {
                    p.vx *= -1;
                }
            }

            p.x = Math.max(0, Math.min(394 - p.width, p.x));
            p.element.style.left = p.x + 'px';
            p.element.style.top = p.y + 'px';

            let hitboxMargin = 4; 
            if (p.y >= (305 - p.width * 0.3) && p.y <= 345 && 
                (p.x + hitboxMargin) < (p1X + 36) && 
                (p.x + p.width - hitboxMargin) > p1X) {
                handleCollision(p);
                p.element.remove();
                poops.splice(i, 1);
                continue;
            }

            if (p.y > boardHeight || p.y < -180 || p.x < -40 || p.x > 450) {
                p.element.remove();
                poops.splice(i, 1);
            }
        }
        updateHUD();
    }

    function spawnExplosiveFragments(x, y) {
        [-2.2, -0.9, 0.9, 2.2].forEach(vxVal => {
            let fragElem = document.createElement('div');
            fragElem.classList.add('poop', 'poop-bad');
            fragElem.innerText = '💥';
            fragElem.style.width = '20px';
            fragElem.style.height = '20px';
            fragElem.style.fontSize = '11px';
            document.getElementById('game-board').appendChild(fragElem);

            poops.push({
                element: fragElem,
                x: x,
                y: y,
                width: 20,
                speed: 1.8,
                vx: vxVal,
                type: 'shrapnel',
                isGood: false,
                name: '폭발 파편',
                icon: '💥'
            });
        });
    }

    function spawnMutantFragments(x, y) {
        [-1.0, 0, 1.0].forEach(vxVal => {
            let fragElem = document.createElement('div');
            fragElem.classList.add('poop', 'poop-bad');
            fragElem.innerText = '☢️';
            fragElem.style.width = '18px';
            fragElem.style.height = '18px';
            fragElem.style.fontSize = '10px';
            document.getElementById('game-board').appendChild(fragElem);

            poops.push({
                element: fragElem,
                x: x,
                y: y,
                width: 18,
                vx: vxVal,
                vy: -3.2, 
                type: 'mutant_fragment',
                isGood: false,
                name: '돌연변이 파편',
                icon: '☢️',
                splitDone: false
            });
        });
    }

    function spawnSecondMutantFragments(x, y) {
        [-1.3, 1.3].forEach(vxVal => {
            let fragElem = document.createElement('div');
            fragElem.classList.add('poop', 'poop-bad');
            fragElem.innerText = '🔥';
            fragElem.style.width = '16px';
            fragElem.style.height = '16px';
            fragElem.style.fontSize = '9px';
            document.getElementById('game-board').appendChild(fragElem);

            poops.push({
                element: fragElem,
                x: x,
                y: y,
                width: 16,
                vx: vxVal,
                vy: 0.5,
                type: 'second_fragment',
                isGood: false,
                name: '2차 연쇄 폭발 파편',
                icon: '🔥'
            });
        });
    }

    function spawnSplitFragments(x, y) {
        [-1.8, 1.8].forEach(dir => {
            let fragElem = document.createElement('div');
            fragElem.classList.add('poop', 'poop-bad');
            fragElem.innerText = '🦠';
            fragElem.style.width = '22px';
            fragElem.style.height = '22px';
            fragElem.style.fontSize = '12px';
            document.getElementById('game-board').appendChild(fragElem);

            poops.push({
                element: fragElem,
                x: x,
                y: y,
                width: 22,
                speed: 1.8,
                vx: dir,
                type: 'shrapnel',
                isGood: false,
                name: '분열 똥 조각',
                icon: '🦠'
            });
        });
    }

    function spawnMultiSplitFragments(x, y) {
        [-2.2, -0.9, 0.9, 2.2].forEach(vxVal => {
            let fragElem = document.createElement('div');
            fragElem.classList.add('poop', 'poop-bad');
            fragElem.innerText = '🧬';
            fragElem.style.width = '20px';
            fragElem.style.height = '20px';
            fragElem.style.fontSize = '11px';
            document.getElementById('game-board').appendChild(fragElem);

            poops.push({
                element: fragElem,
                x: x,
                y: y,
                width: 20,
                speed: 2.0,
                vx: vxVal,
                type: 'shrapnel',
                isGood: false,
                name: '돌연변이 분열 조각',
                icon: '🧬'
            });
        });
    }

    function handleCollision(p) {
        if (p.isGood) {
            playItemSound(true);
            if (p.type === 'heal') {
                if (p1Lives < 5) p1Lives++;
            } else if (p.type === 'rainbow') {
                score += 150;
            }
        } else {
            playItemSound(false);
            lastKiller = { name: p.name, icon: p.icon };
            
            if (p.type === 'reverse') {
                p1Reversed = true;
                let pElem = document.getElementById('player1');
                pElem.classList.add('reversed');
                if (p1Timers.rev) clearTimeout(p1Timers.rev);
                p1Timers.rev = setTimeout(() => {
                    p1Reversed = false;
                    pElem.classList.remove('reversed');
                }, 5000);
            } else if (p.type === 'ice') {
                p1Slowed = true;
                let pElem = document.getElementById('player1');
                pElem.classList.add('slowed');
                if (p1Timers.slow) clearTimeout(p1Timers.slow);
                p1Timers.slow = setTimeout(() => {
                    p1Slowed = false;
                    pElem.classList.remove('slowed');
                }, 5000);
            } else if (p.type === 'blackout') {
                let overlay = document.getElementById('blackout-overlay');
                overlay.style.display = 'block';
                if (p1Timers.blackout) clearTimeout(p1Timers.blackout);
                p1Timers.blackout = setTimeout(() => {
                    overlay.style.display = 'none';
                }, 4000);
            }

            if (!isGodMode) {
                playHitSound();
                let damage = 1;
                if (p.type === 'bomb') damage = 2;
                if (p.type === 'skull_bomb') damage = 3;
                p1Lives -= damage;

                if (p1Lives <= 0) {
                    triggerGameOver();
                }
            }
        }
    }

    function showWaveBanner(text) {
        let banner = document.getElementById('wave-banner');
        banner.innerText = text;
        banner.style.display = 'block';
        setTimeout(() => { banner.style.display = 'none'; }, 2000);
    }

    function triggerGameOver() {
        stopBGM();
        if (gameLoopId) cancelAnimationFrame(gameLoopId);
        hideAllScreens();
        document.getElementById('screen-gameover').classList.add('active');

        document.getElementById('gameover-title').innerText = '💩 게임 오버!';
        document.getElementById('killer-info').innerText = `최후를 선사한 똥: ${lastKiller.icon} ${lastKiller.name}`;
        document.getElementById('final-score').innerText = `최종 점수: ${Math.floor(score)}점`;
        document.getElementById('cheer-msg').innerText = '크레이지 모드는 역시 너무 맵군요!';
        document.getElementById('ranking-prompt-area').style.display = 'block';
        document.getElementById('ranking-name-input').value = '';
        document.getElementById('ranking-status-msg').style.display = 'none';
    }

    function submitRankingYes() {
        if (isAdminLoggedIn) {
            alert('🚫 관리자는 랭킹에 절대로 등록할 수 없습니다!');
            document.getElementById('ranking-prompt-area').style.display = 'none';
            let statusMsg = document.getElementById('ranking-status-msg');
            statusMsg.innerText = '❌ 관리자 계정은 랭킹 등록이 차단되었습니다.';
            statusMsg.style.display = 'block';
            return;
        }

        let nameInput = document.getElementById('ranking-name-input').value.trim();
        let playerName = nameInput ? nameInput : '익명';

        let rankData = getRankings(selectedTier);
        if (!rankData[selectedSubDiff]) rankData[selectedSubDiff] = [];
        rankData[selectedSubDiff].push({ name: playerName, score: Math.floor(score), date: new Date().toLocaleDateString() });
        rankData[selectedSubDiff].sort((a, b) => b.score - a.score);
        if (rankData[selectedSubDiff].length > 5) rankData[selectedSubDiff].pop();
        saveRankings(selectedTier, rankData);

        document.getElementById('ranking-prompt-area').style.display = 'none';
        let statusMsg = document.getElementById('ranking-status-msg');
        statusMsg.innerText = '✅ 랭킹에 성공적으로 등록되었습니다!';
        statusMsg.style.display = 'block';
        displayRankings();
    }

    function submitRankingNo() {
        document.getElementById('ranking-prompt-area').style.display = 'none';
        let statusMsg = document.getElementById('ranking-status-msg');
        statusMsg.innerText = '⏭️ 랭킹 등록을 건너뛰었습니다.';
        statusMsg.style.display = 'block';
    }

    // 키보드 이벤트
    window.addEventListener('keydown', (e) => {
        if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') p1Left = true;
        if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') p1Str = true;
    });

    window.addEventListener('keyup', (e) => {
        if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') p1Left = false;
        if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') p1Str = false;
    });

    // 모바일 터치 및 드래그 이벤트 처리
    const gameBoard = document.getElementById('game-board');

    function handleTouchMove(e) {
        if (!document.getElementById('screen-game').classList.contains('active')) return;
        let clientX = e.touches ? e.touches[0].clientX : e.clientX;
        let rect = gameBoard.getBoundingClientRect();
        let boardX = clientX - rect.left;
        touchTargetX = Math.max(18, Math.min(rect.width - 18, boardX));
        // 터치 좌표를 전체 보드 비율(420px 기준)로 환산
        touchTargetX = (touchTargetX / rect.width) * 420;
    }

    gameBoard.addEventListener('touchstart', (e) => {
        isTouching = true;
        handleTouchMove(e);
    }, { passive: true });

    gameBoard.addEventListener('touchmove', (e) => {
        if (isTouching) handleTouchMove(e);
    }, { passive: true });

    gameBoard.addEventListener('touchend', () => {
        isTouching = false;
    });

    gameBoard.addEventListener('touchcancel', () => {
        isTouching = false;
    });

    // 마우스 드래그 지원 (PC 겸용)
    gameBoard.addEventListener('mousedown', (e) => {
        isTouching = true;
        handleTouchMove(e);
    });

    window.addEventListener('mousemove', (e) => {
        if (isTouching) handleTouchMove(e);
    });

    window.addEventListener('mouseup', () => {
        isTouching = false;
    });
</script>
</body>
</html>
