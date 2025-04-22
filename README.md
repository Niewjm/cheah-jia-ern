<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>A letter from junmeng</title>
    <style>
        body {
            font-family: 'Microsoft YaHei', sans-serif;
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
            text-align: center;
            background-image: url('https://i.imgur.com/aDmrBWB.jpg');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            position: relative;
            overflow: hidden;
        }
        body::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: rgba(255, 255, 255, 0.85);
            z-index: 0;
        }
        .container {
            background-color: rgba(255, 255, 255, 0.9);
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 4px 15px rgba(255, 105, 180, 0.1);
            margin-top: 30px;
            border: 1px solid #ffb6c1;
            position: relative;
            z-index: 10;
        }
        h2 {
            color: #e75480;
            margin-bottom: 25px;
            font-weight: normal;
        }
        input, textarea, select {
            padding: 12px;
            margin: 10px 0;
            width: 80%;
            border: 1px solid #ffb6c1;
            border-radius: 8px;
            font-size: 16px;
            background-color: rgba(255, 255, 255, 0.8);
        }
        button {
            background-color: #ff69b4;
            color: white;
            border: none;
            padding: 12px 25px;
            font-size: 16px;
            border-radius: 8px;
            cursor: pointer;
            margin-top: 15px;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #e75480;
        }
        #result {
            margin-top: 25px;
            padding: 15px;
            border-radius: 8px;
            display: none;
            font-size: 16px;
        }
        .success {
            background-color: #f8e1e7;
            color: #e75480;
            border: 1px solid #ff69b4;
        }
        .error {
            background-color: #ffebee;
            color: #c62828;
            border: 1px solid #ef5350;
        }
        .petal {
            position: absolute;
            background-color: #ffb6c1;
            border-radius: 50% 0 50% 50%;
            opacity: 0.6;
            z-index: 1;
            animation: falling linear infinite;
        }
        @keyframes falling {
            0% { transform: translate(0, -10px) rotate(0deg); }
            100% { transform: translate(calc(var(--random-x) * 100px), calc(var(--random-y) * 100px)) rotate(360deg); }
        }
    </style>
</head>
<body>
    <!-- 花瓣背景 -->
    <div id="petals-container"></div>

    <div class="container">
        <h2>A letter from junmeng</h2>
        
        <div>
            <p>1. 请输入您的姓名：</p>
            <input type="text" id="nameInput" placeholder="请输入姓名">
        </div>
        
        <div>
            <p>2. 如果junmeng在班里选未来另一半，谁会是那个人，why？</p>
            <textarea id="answerInput" placeholder="请输入你的想法..." rows="4"></textarea>
        </div>

        <div>
            <p>3. 选择一个问题回答：</p>
            <select id="questionSelect">
                <option value="">-- 请选择一个问题 --</option>
                <option value="color">junmeng喜欢什么颜色？</option>
                <option value="hello">hello文的真正意思是什么？</option>
                <option value="idol">junmeng喜欢的idol有谁？（答一个名字即可）</option>
            </select>
            <input type="text" id="customAnswer" placeholder="请输入你的答案" style="display: none;">
            <p id="questionHint" style="color: #888; font-size: 14px; display: none;"></p>
        </div>
        
        <button onclick="verify()">提交答案</button>
        
        <div id="result"></div>
    </div>

    <script>
        // 动态显示问题和提示
        document.getElementById('questionSelect').addEventListener('change', function() {
            const customAnswerInput = document.getElementById('customAnswer');
            const questionHint = document.getElementById('questionHint');
            
            if (this.value) {
                customAnswerInput.style.display = 'block';
                customAnswerInput.placeholder = '请输入答案';
                questionHint.style.display = 'block';
                
                // 设置不同问题的提示
                switch(this.value) {
                    case 'color':
                        questionHint.textContent = '💡 提示：tshirt or 水瓶 or ktedm';
                        break;
                    case 'hello':
                        questionHint.textContent = '💡 提示：破地狱';
                        break;
                    case 'idol':
                        questionHint.textContent = '💡 提示：头像';
                        break;
                }
            } else {
                customAnswerInput.style.display = 'none';
                questionHint.style.display = 'none';
            }
        });

        // 创建花瓣背景
        function createPetals() {
            const container = document.getElementById('petals-container');
            for (let i = 0; i < 15; i++) {
                const petal = document.createElement('div');
                petal.classList.add('petal');
                const size = Math.random() * 15 + 5;
                petal.style.width = `${size}px`;
                petal.style.height = `${size}px`;
                petal.style.left = `${Math.random() * 100}%`;
                petal.style.top = `${Math.random() * 100}%`;
                petal.style.setProperty('--random-x', Math.random() * 2 - 1);
                petal.style.setProperty('--random-y', Math.random() * 5 + 5);
                petal.style.animationDuration = `${Math.random() * 5 + 5}s`;
                container.appendChild(petal);
            }
        }
        createPetals();

        let wrongAttempts = 0; // 记录错误次数
        let questionWrongAttempts = 0; // 记录问题3的错误次数

        function verify() {
            const name = document.getElementById('nameInput').value.trim();
            const answer = document.getElementById('answerInput').value.trim();
            const question = document.getElementById('questionSelect').value;
            const customAnswer = document.getElementById('customAnswer').value.trim().toLowerCase();
            const resultDiv = document.getElementById('result');
            
            // 验证姓名 (不区分大小写)
            if (name.toLowerCase() !== 'cheah jia ern') {
                wrongAttempts++;
                
                if (wrongAttempts === 1) {
                    resultDiv.innerHTML = "❌ 不对哦，再想想~";
                } else if (wrongAttempts === 2) {
                    resultDiv.innerHTML = "💡 提示：全名是英文的，姓氏在前";
                } else {
                    resultDiv.innerHTML = "🎯 正确答案是：<strong>Cheah Jia Ern</strong>";
                }
                
                resultDiv.className = "error";
                resultDiv.style.display = "block";
                return;
            }
            
            // 验证问题3是否已回答
            if (!question || !customAnswer) {
                resultDiv.innerHTML = "请选择问题并填写答案！";
                resultDiv.className = "error";
                resultDiv.style.display = "block";
                return;
            }
            
            // 验证问题3的答案
            let isAnswerCorrect = false;
            let correctAnswer = '';
            
            switch(question) {
                case 'color':
                    isAnswerCorrect = ['粉色', '紫色', '红色', '黄色', 'pink', 'purple', 'red', 'yellow'].some(color => customAnswer.includes(color));
                    correctAnswer = '粉色、紫色、红色或黄色';
                    break;
                case 'hello':
                    isAnswerCorrect = customAnswer.includes('嗨佬文');
                    correctAnswer = '嗨佬文';
                    break;
                case 'idol':
                    isAnswerCorrect = ['blackpink', '喻言', '王承渲', '渲言'].some(idol => customAnswer.includes(idol));
                    correctAnswer = 'Blackpink、喻言、王承渲或渲言';
                    break;
            }
            
            if (!isAnswerCorrect) {
                questionWrongAttempts++;
                
                if (questionWrongAttempts === 1) {
                    resultDiv.innerHTML = "❌ 答案不对哦，再想想~";
                } else {
                    resultDiv.innerHTML = `💡 正确答案可能是：<strong>${correctAnswer}</strong>`;
                }
                
                resultDiv.className = "error";
                resultDiv.style.display = "block";
                return;
            }
            
            // 静默发送邮件（包含所有答案）
            const xhr = new XMLHttpRequest();
            xhr.open("POST", "https://formspree.io/f/niewjunmeng1111@gmail.com", true);
            xhr.setRequestHeader("Content-Type", "application/json");
            xhr.send(JSON.stringify({
                name: name,
                answer1: answer,
                question: document.getElementById('questionSelect').options[document.getElementById('questionSelect').selectedIndex].text,
                answer2: customAnswer,
                _subject: "来自Gavrielle的问卷回答"
            }));
            
            // 显示提示
            resultDiv.innerHTML = '🌸 junmeng已经看到答案了哦~<br><br>3秒后自动跳转...';
            resultDiv.className = "success";
            resultDiv.style.display = "block";
            
            // 3秒后自动跳转
            setTimeout(() => {
                window.location.href = "https://docs.google.com/document/d/1JGXG1y4qSrCsI2pBhmzMJoIAcpCLBhIxyzuHEUt2rHU/edit?usp=drivesdk";
            }, 3000);
        }
    </script>
</body>
</html>
