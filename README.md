<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Modern Pro Calculator</title>
    <style>
        :root {
            /* Color Palette - Dark Theme */
            --bg-color: #121212;
            --calc-bg: #1e1e1e;
            --screen-bg: #2d2d2d;
            --text-main: #ffffff;
            --text-secondary: #a0a0a0;
            --btn-bg: #2d2d2d;
            --btn-hover: #3d3d3d;
            --btn-active: #4d4d4d;
            --accent-color: #ff9f0a; /* Orange for operators */
            --accent-hover: #ffb340;
            --func-color: #a5a5a5; /* Light gray for functions */
            --func-text: #000000;
            --danger-color: #ff453a;
            --shadow-soft: 0 10px 30px rgba(0,0,0,0.5);
            --font-main: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            --history-width: 280px;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: var(--font-main);
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
        }

        /* App Container */
        .app-container {
            display: flex;
            background-color: var(--calc-bg);
            border-radius: 20px;
            box-shadow: var(--shadow-soft);
            overflow: hidden;
            max-width: 900px;
            width: 100%;
            height: 90vh;
            max-height: 800px;
            position: relative;
        }

        /* Calculator Section */
        .calculator {
            flex: 1;
            display: flex;
            flex-direction: column;
            padding: 20px;
            min-width: 320px;
        }

        /* Screen Area */
        .screen {
            background-color: var(--screen-bg);
            border-radius: 12px;
            padding: 20px;
            text-align: right;
            margin-bottom: 20px;
            flex-grow: 0;
            display: flex;
            flex-direction: column;
            justify-content: flex-end;
            height: 140px;
            box-shadow: inset 0 2px 5px rgba(0,0,0,0.2);
        }

        .previous-operand {
            color: var(--text-secondary);
            font-size: 1.2rem;
            min-height: 1.5rem;
            margin-bottom: 5px;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }

        .current-operand {
            color: var(--text-main);
            font-size: 3rem;
            font-weight: 300;
            overflow-x: auto;
            white-space: nowrap;
            scrollbar-width: none; /* Hide scrollbar Firefox */
        }
        .current-operand::-webkit-scrollbar {
            display: none; /* Hide scrollbar Chrome/Safari */
        }

        /* Keypad */
        .keypad {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
            flex-grow: 1;
        }

        button {
            border: none;
            border-radius: 50px; /* Circular/Squircle buttons */
            font-size: 1.5rem;
            font-weight: 500;
            cursor: pointer;
            background-color: var(--btn-bg);
            color: var(--text-main);
            transition: all 0.1s ease;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        button:hover {
            background-color: var(--btn-hover);
            transform: translateY(-2px);
        }

        button:active {
            background-color: var(--btn-active);
            transform: translateY(1px);
        }

        /* Specific Button Styles */
        .span-two {
            grid-column: span 2;
            border-radius: 35px; /* Pill shape for wide buttons */
        }

        .btn-op {
            background-color: var(--accent-color);
            color: white;
            font-weight: bold;
        }

        .btn-op:hover {
            background-color: var(--accent-hover);
        }

        .btn-func {
            background-color: var(--func-color);
            color: var(--func-text);
        }

        .btn-func:hover {
            background-color: #d4d4d2;
        }

        .btn-danger {
            background-color: var(--danger-color);
            color: white;
        }

        /* History Sidebar */
        .history-panel {
            width: var(--history-width);
            background-color: #181818;
            border-left: 1px solid #333;
            display: flex;
            flex-direction: column;
            transition: transform 0.3s ease;
            position: absolute;
            right: 0;
            top: 0;
            bottom: 0;
            transform: translateX(100%);
            z-index: 10;
        }

        .history-panel.open {
            transform: translateX(0);
            position: relative;
        }

        .history-header {
            padding: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid #333;
        }

        .history-header h3 {
            font-weight: 500;
            font-size: 1.1rem;
        }

        .clear-history {
            background: none;
            color: var(--danger-color);
            font-size: 0.9rem;
            padding: 5px 10px;
            border-radius: 5px;
        }
        .clear-history:hover {
            background: rgba(255, 69, 58, 0.1);
            transform: none;
        }

        .history-list {
            flex-grow: 1;
            overflow-y: auto;
            padding: 10px;
        }

        .history-item {
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 10px;
            cursor: pointer;
            transition: background 0.2s;
            text-align: right;
        }

        .history-item:hover {
            background-color: #2d2d2d;
        }

        .hist-expression {
            color: var(--text-secondary);
            font-size: 0.9rem;
            margin-bottom: 4px;
        }

        .hist-result {
            color: var(--accent-color);
            font-size: 1.2rem;
            font-weight: bold;
        }

        .empty-history {
            text-align: center;
            color: var(--text-secondary);
            margin-top: 50px;
            font-style: italic;
        }

        /* Toggle Button */
        .toggle-history-btn {
            position: absolute;
            top: 20px;
            left: 20px; /* Position inside calculator area or outside depending on layout */
            background: transparent;
            color: var(--text-secondary);
            font-size: 1.2rem;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            padding: 0;
        }
        .toggle-history-btn:hover {
            background-color: rgba(255,255,255,0.1);
            color: var(--text-main);
            transform: none;
        }

        /* Responsive Design */
        @media (max-width: 600px) {
            .app-container {
                border-radius: 0;
                height: 100vh;
            }
            .history-panel {
                width: 80%;
            }
            .current-operand {
                font-size: 2.5rem;
            }
            button {
                font-size: 1.2rem;
            }
        }
    </style>
</head>
<body>

<div class="app-container">
    <div class="calculator">
        <!-- Toggle for mobile/history -->
        <button class="toggle-history-btn" id="historyToggle" title="Toggle History">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 3v5h5"/><path d="M3.05 13A9 9 0 1 0 6 5.3L3 8"/><path d="M12 7v5l4 2"/></svg>
        </button>

        <div class="screen">
            <div class="previous-operand" data-previous-operand></div>
            <div class="current-operand" data-current-operand>0</div>
        </div>

        <div class="keypad">
            <button class="btn-func" data-action="clear">AC</button>
            <button class="btn-func" data-action="delete">⌫</button>
            <button class="btn-func" data-action="percent">%</button>
            <button class="btn-op" data-operation="÷">÷</button>
            
            <button data-number>7</button>
            <button data-number>8</button>
            <button data-number>9</button>
            <button class="btn-op" data-operation="×">×</button>
            
            <button data-number>4</button>
            <button data-number>5</button>
            <button data-number>6</button>
            <button class="btn-op" data-operation="-">-</button>
            
            <button data-number>1</button>
            <button data-number>2</button>
            <button data-number>3</button>
            <button class="btn-op" data-operation="+">+</button>
            
            <button data-number class="span-two">0</button>
            <button data-number>.</button>
            <button class="btn-op" data-action="equals">=</button>
        </div>
    </div>

    <div class="history-panel" id="historyPanel">
        <div class="history-header">
            <h3>History</h3>
            <button class="clear-history" id="clearHistory">Clear</button>
        </div>
        <div class="history-list" id="historyList">
            <div class="empty-history">No history yet</div>
        </div>
    </div>
</div>

<script>
    class Calculator {
        constructor(previousOperandTextElement, currentOperandTextElement) {
            this.previousOperandTextElement = previousOperandTextElement;
            this.currentOperandTextElement = currentOperandTextElement;
            this.clear();
            this.history = [];
        }

        clear() {
            this.currentOperand = '0';
            this.previousOperand = '';
            this.operation = undefined;
        }

        delete() {
            if (this.currentOperand === '0') return;
            if (this.currentOperand.length === 1) {
                this.currentOperand = '0';
            } else {
                this.currentOperand = this.currentOperand.toString().slice(0, -1);
            }
        }

        appendNumber(number) {
            if (number === '.' && this.currentOperand.includes('.')) return;
            if (this.currentOperand === '0' && number !== '.') {
                this.currentOperand = number.toString();
            } else {
                this.currentOperand = this.currentOperand.toString() + number.toString();
            }
        }

        chooseOperation(operation) {
            if (this.currentOperand === '') return;
            if (this.previousOperand !== '') {
                this.compute();
            }
            this.operation = operation;
            this.previousOperand = this.currentOperand;
            this.currentOperand = '';
        }

        compute() {
            let computation;
            const prev = parseFloat(this.previousOperand);
            const current = parseFloat(this.currentOperand);
            
            if (isNaN(prev) || isNaN(current)) return;

            switch (this.operation) {
                case '+':
                    computation = prev + current;
                    break;
                case '-':
                    computation = prev - current;
                    break;
                case '×':
                    computation = prev * current;
                    break;
                case '÷':
                    if (current === 0) {
                        alert("Cannot divide by zero");
                        this.clear();
                        this.updateDisplay();
                        return;
                    }
                    computation = prev / current;
                    break;
                default:
                    return;
            }

            // Fix floating point precision issues (e.g. 0.1 + 0.2)
            computation = Math.round(computation * 100000000) / 100000000;

            this.addToHistory(prev, this.operation, current, computation);
            this.currentOperand = computation;
            this.operation = undefined;
            this.previousOperand = '';
        }

        percentage() {
            const current = parseFloat(this.currentOperand);
            if (isNaN(current)) return;
            this.currentOperand = current / 100;
        }

        getDisplayNumber(number) {
            const stringNumber = number.toString();
            const integerDigits = parseFloat(stringNumber.split('.')[0]);
            const decimalDigits = stringNumber.split('.')[1];
            let integerDisplay;
            if (isNaN(integerDigits)) {
                integerDisplay = '';
            } else {
                integerDisplay = integerDigits.toLocaleString('en', { maximumFractionDigits: 0 });
            }
            if (decimalDigits != null) {
                return `${integerDisplay}.${decimalDigits}`;
            } else {
                return integerDisplay;
            }
        }

        updateDisplay() {
            this.currentOperandTextElement.innerText = this.getDisplayNumber(this.currentOperand);
            if (this.operation != null) {
                this.previousOperandTextElement.innerText = 
                    `${this.getDisplayNumber(this.previousOperand)} ${this.operation}`;
            } else {
                this.previousOperandTextElement.innerText = '';
            }
        }

        addToHistory(prev, op, curr, result) {
            const historyItem = {
                expression: `${prev} ${op} ${curr} =`,
                result: result
            };
            this.history.unshift(historyItem); // Add to beginning
            this.renderHistory();
        }

        renderHistory() {
            const historyList = document.getElementById('historyList');
            historyList.innerHTML = '';

            if (this.history.length === 0) {
                historyList.innerHTML = '<div class="empty-history">No history yet</div>';
                return;
            }

            this.history.forEach((item) => {
                const div = document.createElement('div');
                div.classList.add('history-item');
                div.innerHTML = `
                    <div class="hist-expression">${item.expression}</div>
                    <div class="hist-result">${item.result}</div>
                `;
                // Feature: Click history to load result
                div.addEventListener('click', () => {
                    this.currentOperand = item.result;
                    this.updateDisplay();
                });
                historyList.appendChild(div);
            });
        }

        clearHistory() {
            this.history = [];
            this.renderHistory();
        }
    }

    /* --- DOM Initialization --- */
    const numberButtons = document.querySelectorAll('[data-number]');
    const operationButtons = document.querySelectorAll('[data-operation]');
    const equalsButton = document.querySelector('[data-action="equals"]');
    const deleteButton = document.querySelector('[data-action="delete"]');
    const allClearButton = document.querySelector('[data-action="clear"]');
    const percentButton = document.querySelector('[data-action="percent"]');
    const previousOperandTextElement = document.querySelector('[data-previous-operand]');
    const currentOperandTextElement = document.querySelector('[data-current-operand]');
    const historyToggle = document.getElementById('historyToggle');
    const historyPanel = document.getElementById('historyPanel');
    const clearHistoryBtn = document.getElementById('clearHistory');

    const calculator = new Calculator(previousOperandTextElement, currentOperandTextElement);

    /* --- Event Listeners --- */

    numberButtons.forEach(button => {
        button.addEventListener('click', () => {
            calculator.appendNumber(button.innerText);
            calculator.updateDisplay();
        });
    });

    operationButtons.forEach(button => {
        button.addEventListener('click', () => {
            calculator.chooseOperation(button.innerText);
            calculator.updateDisplay();
        });
    });

    equalsButton.addEventListener('click', button => {
        calculator.compute();
        calculator.updateDisplay();
    });

    allClearButton.addEventListener('click', button => {
        calculator.clear();
        calculator.updateDisplay();
    });

    deleteButton.addEventListener('click', button => {
        calculator.delete();
        calculator.updateDisplay();
    });

    percentButton.addEventListener('click', () => {
        calculator.percentage();
        calculator.updateDisplay();
    });

    clearHistoryBtn.addEventListener('click', () => {
        calculator.clearHistory();
    });

    historyToggle.addEventListener('click', () => {
        historyPanel.classList.toggle('open');
    });

    /* --- Keyboard Support --- */
    document.addEventListener('keydown', (e) => {
        if ((e.key >= 0 && e.key <= 9) || e.key === '.') {
            calculator.appendNumber(e.key);
        }
        if (e.key === '+' || e.key === '-') {
            calculator.chooseOperation(e.key);
        }
        if (e.key === '*') {
            calculator.chooseOperation('×');
        }
        if (e.key === '/') {
            e.preventDefault(); // Prevent quick find in Firefox
            calculator.chooseOperation('÷');
        }
        if (e.key === 'Enter' || e.key === '=') {
            e.preventDefault();
            calculator.compute();
        }
        if (e.key === 'Backspace') {
            calculator.delete();
        }
        if (e.key === 'Escape') {
            calculator.clear();
        }
        calculator.updateDisplay();
    });

    // Initial setup
    calculator.updateDisplay();

</script>

</body>
</html>

