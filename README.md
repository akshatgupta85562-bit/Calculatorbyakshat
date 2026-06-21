# Calculatorbyakshat
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>The Ultimate Scan Calculator</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #0d0d13, #151526);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            color: #fff;
            padding: 20px;
            position: relative;
            overflow: hidden;
        }

        .calc-container {
            background: #1e1e38;
            padding: 25px;
            border-radius: 24px;
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.7);
            width: 100%;
            max-width: 380px;
            border: 2px solid #3f3f61;
            z-index: 1;
        }

        .screen {
            background: #080811;
            padding: 20px;
            border-radius: 16px;
            text-align: right;
            margin-bottom: 20px;
            min-height: 130px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            word-break: break-all;
            border: 1px solid #2e2e4a;
        }

        .input-display {
            font-size: 1.3rem;
            color: #8c8cb3;
            height: 28px;
        }

        .result-display {
            font-size: 1.5rem;
            font-weight: bold;
            color: #00ffcc;
            margin-top: 10px;
            transition: all 0.3s ease;
            line-height: 1.4;
        }

        .loading-text {
            font-size: 1.2rem;
            color: #ffb703;
            font-style: italic;
            animation: blink 1s infinite;
            text-align: center;
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100%;
        }

        @keyframes blink {
            0% { opacity: 0.6; }
            50% { opacity: 1; }
            100% { opacity: 0.6; }
        }

        .buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
        }

        button {
            padding: 18px;
            font-size: 1.3rem;
            font-weight: 600;
            border: none;
            border-radius: 14px;
            background: #2e2e4a;
            color: #fff;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        button:active {
            transform: scale(0.93);
        }

        button:hover {
            background: #44446c;
            color: #00ffcc;
        }

        button.operator {
            background: #f72585;
            color: #fff;
        }

        button.operator:hover {
            background: #b5179e;
        }

        button.clear {
            background: #e63946;
            color: #fff;
        }

        button.clear:hover {
            background: #d90429;
        }

        button.equal {
            background: #00ffcc;
            color: #080811;
            grid-column: span 2;
        }

        button.equal:hover {
            background: #00b399;
        }

        .footer {
            text-align: center;
            margin-top: 25px;
            font-size: 0.85rem;
            color: #7a7a9a;
            letter-spacing: 0.5px;
            border-top: 1px solid #2e2e4a;
            padding-top: 15px;
        }

        .footer strong {
            color: #ffb703;
        }

        /* नकली यूपीआई पेमेंट गेटवे स्क्रीन */
        .pay-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.85);
            z-index: 10;
            display: flex;
            justify-content: center;
            align-items: center;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.4s ease;
        }

        .pay-overlay.active {
            opacity: 1;
            pointer-events: auto;
        }

        .gpay-box {
            background: #ffffff;
            color: #333;
            width: 90%;
            max-width: 340px;
            border-radius: 20px;
            padding: 25px;
            text-align: center;
            box-shadow: 0 20px 50px rgba(0,0,0,0.5);
            transform: scale(0.8);
            transition: transform 0.4s ease;
        }

        .pay-overlay.active .gpay-box {
            transform: scale(1);
        }

        .gpay-header {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 8px;
            margin-bottom: 20px;
        }

        .gpay-logo {
            font-size: 1.6rem;
            font-weight: bold;
            color: #1a73e8;
        }

        .gpay-logo span { color: #ea4335; }
        .gpay-logo span.g2 { color: #fbbc05; }
        .gpay-logo span.g3 { color: #34a853; }

        .payment-details {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 12px;
            margin-bottom: 25px;
            border: 1px dashed #ced4da;
        }

        .amount {
            font-size: 2.2rem;
            font-weight: 700;
            color: #202124;
            margin: 10px 0;
        }

        .pay-btn {
            background: #1a73e8;
            color: white;
            width: 100%;
            padding: 15px;
            font-size: 1.1rem;
            border: none;
            border-radius: 50px;
            font-weight: 600;
            cursor: pointer;
            box-shadow: 0 4px 12px rgba(26,115,232,0.3);
            transition: all 0.2s;
        }

        .pay-btn:active {
            transform: scale(0.96);
        }

        .success-checkmark {
            color: #34a853;
            font-size: 4rem;
            display: none;
            animation: pop 0.4s ease;
        }

        @keyframes pop {
            0% { transform: scale(0); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body>

    <div class="calc-container">
        <div class="screen">
            <div class="input-display" id="input"></div>
            <div class="result-display" id="result">0</div>
        </div>

        <div class="buttons">
            <button class="clear" onclick="clearScreen()">C</button>
            <button onclick="appendValue('(')">(</button>
            <button onclick="appendValue(')')">)</button>
            <button class="operator" onclick="appendValue('/')">/</button>

            <button onclick="appendValue('7')">7</button>
            <button onclick="appendValue('8')">8</button>
            <button onclick="appendValue('9')">9</button>
            <button class="operator" onclick="appendValue('*')">×</button>

            <button onclick="appendValue('4')">4</button>
            <button onclick="appendValue('5')">5</button>
            <button onclick="appendValue('6')">6</button>
            <button class="operator" onclick="appendValue('-')">-</button>

            <button onclick="appendValue('1')">1</button>
            <button onclick="appendValue('2')">2</button>
            <button onclick="appendValue('3')">3</button>
            <button class="operator" onclick="appendValue('+')">+</button>

            <button onclick="appendValue('0')">0</button>
            <button onclick="appendValue('.')">.</button>
            <button class="equal" onclick="startPrank()">=</button>
        </div>

        <div class="footer">
            Developer: <strong>Akshat Gupta, Vikrampur</strong>
        </div>
    </div>

    <div class="pay-overlay" id="payOverlay">
        <div class="gpay-box" id="gpayBox">
            <div id="paymentForm">
                <div class="gpay-header">
                    <div class="gpay-logo">G<span>o</span>o<span>g</span>l<span>e</span> Pay</div>
                </div>
                <div class="payment-details">
                    <p style="color: #5f6368; font-size: 0.9rem;">Paying to</p>
                    <p style="font-weight: 600; color: #202124; margin-top: 2px;">Prank Calc Server Studio</p>
                    <div class="amount" id="dynamicAmount">₹1,499.00</div>
                    <p style="color: #5f6368; font-size: 0.8rem;">Banking System: Auto-Debit Protocol Enabled</p>
                </div>
                <button class="pay-btn" id="payBtn" onclick="processFakePayment()">Proceed to Pay</button>
            </div>
            
            <div id="successState" style="display: none;">
                <div class="success-checkmark">✔</div>
                <h3 style="color: #202124; margin-top: 15px;">Payment Successful</h3>
                <p style="color: #5f6368; font-size: 0.9rem; margin-top: 5px;">Money deducted from your linked bank account.</p>
            </div>
        </div>
    </div>

    <script>
        let inputArea = document.getElementById('input');
        let resultArea = document.getElementById('result');
        let overlay = document.getElementById('payOverlay');
        let isProcessing = false;
        let realAnswer = "";

        const loadingPhrases = [
            "Calling Elon Musk...",
            "Asking Mukesh Ambani for loan...",
            "Connecting to Narendra Modi...",
            "Asking Akshat Gupta for permission...",
            "Decrypting secure protocols...",
            "Connecting to Bank Server API..."
        ];

        const funnyAnswers = [
            "404 Error: Dimaag nahi mila!",
            "Rs. 150 Dega!",
            "Tumse na ho payega 😂",
            "Tera ghar jaayenga isme! 🏠",
            "Melody khao, khud jaan jao!",
            "Jal lijiye, thak gaye honge 🥛",
            "Paisa hi Paisa hoga! 🤑",
            "Chilla chilla ke sabko scheme bata de!",
            "Yeh baburao ka style hai! 😎",
            "Jaldi wahan se hato! 🚨",
            "Systumm hang ho gaya bhai! 🔥",
            "Khatam, Bye Bye, Tata, Goodbye! 👋",
            "Gajab bejjati hai yaar! 😭"
        ];

        // ₹1000 से ₹2000 के बीच रैंडम नकली अमाउंट जेनरेट करने के लिए
        const fakeAmounts = ["₹1,250.00", "₹1,499.00", "₹1,799.00", "₹1,999.00"];

        function appendValue(val) {
            if (isProcessing) return;
            if (resultArea.classList.contains('loading-text')) {
                resultArea.classList.remove('loading-text');
                resultArea.innerText = '0';
            }
            inputArea.innerText += val;
        }

        function clearScreen() {
            if (isProcessing) return;
            inputArea.innerText = '';
            resultArea.innerText = '0';
            resultArea.className = 'result-display';
        }

        function startPrank() {
            let expr = inputArea.innerText;
            if (expr === '' || isProcessing) return;

            // बैकग्राउंड में असली मैथ आंसर कैलकुलेट कर लेते हैं ताकि बाद में दिखा सकें
            try {
                // सुरक्षित रिप्लेसमेंट (× को * और / को / रखने के लिए)
                let sanitizeExpr = expr.replace(/×/g, '*');
                realAnswer = eval(sanitizeExpr);
                if(realAnswer === undefined || isNaN(realAnswer)) realAnswer = "0";
            } catch (e) {
                realAnswer = "Syntax Error";
            }

            isProcessing = true;
            resultArea.className = 'result-display loading-text';
            
            let count = 0;
            let usedPhrases = [];

            // 10 सेकंड का सस्पेंस ड्रामा (4 डायलॉग, हर डायलॉग 2.5 सेकंड)
            let loadingInterval = setInterval(() => {
                let available = loadingPhrases.filter(p => !usedPhrases.includes(p));
                let phrase = available[Math.floor(Math.random() * available.length)];
                usedPhrases.push(phrase);
                resultArea.innerText = phrase;
                count++;

                if (count >= 4) {
                    clearInterval(loadingInterval);
                    
                    // लोडिंग के ठीक बाद यूपीआई पेमेंट गेटवे स्क्रीन को एक्टिवेट करना
                    setTimeout(() => {
                        document.getElementById('dynamicAmount').innerText = fakeAmounts[Math.floor(Math.random() * fakeAmounts.length)];
                        document.getElementById('paymentForm').style.display = 'block';
                        document.getElementById('successState').style.display = 'none';
                        overlay.classList.add('active');
                    }, 2000);
                }
            }, 2500);
        }

        function processFakePayment() {
            let btn = document.getElementById('payBtn');
            btn.innerText = "Processing Secure Payment...";
            btn.style.background = "#5f6368";
            btn.disabled = true;

            // 3 सेकंड तक पैसे कटने की एक्टिंग (स्पिनर फील)
            setTimeout(() => {
                document.getElementById('paymentForm').style.display = 'none';
                document.getElementById('successState').style.display = 'block';
                document.querySelector('.success-checkmark').style.display = 'inline-block';

                // सक्सेस स्क्रीन को 2.5 सेकंड दिखाने के बाद बंद करना और जोक + आंसर डिस्प्ले करना
                setTimeout(() => {
                    overlay.classList.remove('active');
                    
                    // कैलकुलेटर स्क्रीन को वापस नॉर्मल करना
                    resultArea.className = 'result-display';
                    let randomJoke = funnyAnswers[Math.floor(Math.random() * funnyAnswers.length)];
                    
                    // जोक के साथ ब्रैकेट में असली मैथ आंसर भी दिखा दिया
                    resultArea.innerHTML = `${randomJoke} <br><span style="font-size:1.1rem; color:#8c8cb3;">(Actual Ans: ${realAnswer})</span>`;
                    isProcessing = false;
                }, 2500);

            }, 3000);
        }
    </script>
</body>
</html>
