[reaction-time.html](https://github.com/user-attachments/files/29252203/reaction-time.html)
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>⚡ 反应时间测试</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            user-select: none;
            transition: none;  /* 移除所有过渡 */
        }

        /* 初始状态：紫色背景 */
        .state-waiting {
            background: #667eea;
        }

        /* 准备状态：蓝色背景 */
        .state-ready {
            background: #228be6;
        }

        /* 点击后状态：绿色背景 */
        .state-clicked {
            background: #40c057;
        }

        /* 太快了状态：红色背景 */
        .state-too-soon {
            background: #ee5a52;
        }

        /* 主容器 */
        .container {
            text-align: center;
            color: white;
            padding: 40px;
            max-width: 600px;
        }

        /* 标题 */
        h1 {
            font-size: 48px;
            margin-bottom: 20px;
        }

        /* 说明文字 */
        .instruction {
            font-size: 24px;
            margin-bottom: 30px;
            line-height: 1.6;
        }

        /* 时间显示 */
        .time-display {
            font-size: 72px;
            font-weight: bold;
            margin: 30px 0;
        }

        /* 开始按钮 */
        .start-btn {
            padding: 20px 50px;
            font-size: 24px;
            font-weight: bold;
            border: none;
            border-radius: 50px;
            background: white;
            color: #667eea;
            cursor: pointer;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
            transition: all 0.3s ease;
        }

        .start-btn:hover {
            transform: scale(1.05);
            box-shadow: 0 6px 30px rgba(0, 0, 0, 0.4);
        }

        /* 隐藏类 */
        .hidden {
            display: none;
        }

        /* 历史记录 */
        .history {
            margin-top: 40px;
            padding: 20px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 15px;
        }

        .history h3 {
            font-size: 20px;
            margin-bottom: 15px;
        }

        .history-list {
            list-style: none;
            font-size: 18px;
        }

        .history-list li {
            padding: 8px 0;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
        }

        .history-list li:last-child {
            border-bottom: none;
        }

        /* 评价 */
        .rating {
            font-size: 36px;
            margin: 20px 0;
        }

        /* 重置按钮 */
        .reset-btn {
            padding: 15px 40px;
            font-size: 20px;
            font-weight: bold;
            border: 2px solid white;
            border-radius: 50px;
            background: transparent;
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-top: 20px;
        }

        .reset-btn:hover {
            background: white;
            color: #667eea;
            transform: scale(1.05);
        }
    </style>
</head>
<body class="state-waiting">
    <div class="container">
        <!-- 初始状态：显示开始按钮 -->
        <div id="start-screen">
            <h1>⚡ 反应时间测试</h1>
            <p class="instruction">
                测试你的反应速度！<br>
                看到背景变色后，<strong>立即点击页面</strong>
            </p>
            <button class="start-btn" id="start-btn">开始测试</button>
            <div class="history" id="history-section">
                <h3>📊 历史记录</h3>
                <ul class="history-list" id="history-list">
                    <!-- 历史记录会动态插入这里 -->
                </ul>
            </div>
        </div>

        <!-- 等待状态 -->
        <div id="waiting-screen" class="hidden">
            <h1>⏳ 等待中...</h1>
            <p class="instruction">看到背景变色后立即点击！</p>
        </div>

        <!-- 结果显示状态 -->
        <div id="result-screen" class="hidden">
            <h1 id="result-title"></h1>
            <div class="time-display" id="time-display"></div>
            <div class="rating" id="rating"></div>
            <button class="reset-btn" id="reset-btn">再试一次</button>
        </div>
    </div>

    <script>
        // ===== 全局变量 =====

        let startTime = 0;  // 记录变色开始时间
        let timeoutId = null;  // 用于存储 setTimeout 的 ID
        let reactionTimes = [];  // 存储历史反应时间

        // ===== 获取 DOM 元素 =====

        const body = document.body;
        const startScreen = document.getElementById('start-screen');
        const waitingScreen = document.getElementById('waiting-screen');
        const resultScreen = document.getElementById('result-screen');
        const startBtn = document.getElementById('start-btn');
        const resetBtn = document.getElementById('reset-btn');
        const resultTitle = document.getElementById('result-title');
        const timeDisplay = document.getElementById('time-display');
        const ratingDisplay = document.getElementById('rating');
        const historyList = document.getElementById('history-list');

        // ===== 随机延迟时间（1到5秒之间）=====
        function getRandomDelay() {
            return Math.random() * 4000 + 1000;  // 1000ms 到 5000ms
        }

        // ===== 开始测试 =====
        function startTest() {
            console.log('✅ 开始测试');

            // 切换 class 来控制背景色
            body.className = 'state-ready';

            // 隐藏开始界面，显示等待界面
            startScreen.style.display = 'none';
            waitingScreen.style.display = 'block';

            // 设置随机延迟后变色
            const delay = getRandomDelay();
            console.log('⏱️ 延迟时间：', delay, 'ms');

            timeoutId = setTimeout(() => {
                console.log('⏰ setTimeout 触发了！');
                changeColor();
            }, delay);
        }

        // ===== 背景变色 =====
        function changeColor() {
            console.log('🎨 changeColor() 被调用');

            // 使用 performance.now() 获取高精度时间戳
            startTime = performance.now();
            console.log('📊 记录时间：', startTime);

            // 切换 class 到绿色
            body.className = 'state-clicked';
            console.log('✅ 背景已变绿');

            // 隐藏等待界面
            waitingScreen.style.display = 'none';
            console.log('✅ 等待界面已隐藏');
        }

        // ===== 处理点击 =====
        function handleClick() {
            console.log('🖱️ 点击事件触发');

            // 如果还没变色就点击了（startTime 为 0）
            if (startTime === 0) {
                console.log('⚠️ 太快了！');
                tooSoon();
                return;
            }

            // 使用高精度时间计算
            const endTime = performance.now();
            const reactionTime = Math.round(endTime - startTime);
            console.log('⏱️ 反应时间：', reactionTime, 'ms');

            // 显示结果
            showResult(reactionTime);

            // 重置 startTime，防止重复触发
            startTime = 0;
        }

        // ===== 点太快了 =====
        function tooSoon() {
            // 清除定时器
            if (timeoutId) {
                clearTimeout(timeoutId);
                timeoutId = null;
            }

            // 切换 class 到红色
            body.className = 'state-too-soon';

            // 显示提示
            waitingScreen.style.display = 'none';
            resultScreen.style.display = 'block';
            resultTitle.textContent = '⚠️ 太快了！';
            timeDisplay.textContent = '—';
            ratingDisplay.textContent = '等背景变色后再点击哦';
        }

        // ===== 显示结果 =====
        function showResult(reactionTime) {
            console.log('📊 显示结果');

            // 保存到历史记录
            reactionTimes.push(reactionTime);

            // 隐藏所有界面
            startScreen.style.display = 'none';
            waitingScreen.style.display = 'none';

            // 显示结果界面
            resultScreen.style.display = 'block';

            // 显示时间（毫秒）
            timeDisplay.textContent = reactionTime + ' ms';

            // 根据反应时间给出评价
            let rating = '';
            if (reactionTime < 200) {
                rating = '⚡ 闪电侠！超神反应！';
            } else if (reactionTime < 300) {
                rating = '🔥 非常快！职业选手级别！';
            } else if (reactionTime < 400) {
                rating = '👍 很快！超过大多数人！';
            } else if (reactionTime < 500) {
                rating = '😊 不错！正常水平！';
            } else if (reactionTime < 700) {
                rating = '😐 一般，还可以更快！';
            } else {
                rating = '🐌 有点慢，多练练！';
            }

            ratingDisplay.textContent = rating;

            // 更新历史记录
            updateHistory();
        }

        // ===== 更新历史记录 =====
        function updateHistory() {
            // 清空历史列表
            historyList.innerHTML = '';

            // 显示最近5次成绩（倒序）
            const recentTimes = reactionTimes.slice(-5).reverse();

            recentTimes.forEach((time, index) => {
                const li = document.createElement('li');
                li.textContent = `${reactionTimes.length - index}. ${time} ms`;
                historyList.appendChild(li);
            });
        }

        // ===== 重置测试 =====
        function resetTest() {
            console.log('🔄 重置测试');

            // 清除定时器
            if (timeoutId) {
                clearTimeout(timeoutId);
                timeoutId = null;
            }

            // 重置变量
            startTime = 0;

            // 切换回初始状态
            body.className = 'state-waiting';
            resultScreen.style.display = 'none';
            waitingScreen.style.display = 'none';
            startScreen.style.display = 'block';

            console.log('✅ 重置完成');
        }

        // ===== 事件监听 =====

        // 点击开始按钮
        startBtn.addEventListener('click', startTest);

        // 点击重置按钮
        resetBtn.addEventListener('click', resetTest);

        // 点击整个页面（排除按钮）
        body.addEventListener('click', (event) => {
            // 如果点击的是按钮，不处理
            if (event.target.tagName === 'BUTTON') {
                return;
            }
            handleClick();
        });

        // 页面加载完成日志
        console.log('✅ 页面加载完成，准备就绪！');
    </script>
</body>
</html>
