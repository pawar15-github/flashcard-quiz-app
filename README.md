# flashcard-quiz-app
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flashcard Quiz App</title>
    <link rel="stylesheet" href="styles.css">
    <style>
        body {
            margin: 0;
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background: linear-gradient(135deg, #96deda 0%, #c1eb36 100%);
        }
        .container {
            width: 90%;
            max-width: 800px;
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
            background: rgba(255, 255, 255, 0.9);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        h1 {
            color: #333;
            margin-bottom: 20px;
        }
        .content {
            display: flex;
            justify-content: space-between;
            width: 100%;
        }
        .flashcard-container {
            width: 48%;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .form-container {
            width: 48%;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .card {
            width: 80%;
            max-width: 300px;
            height: 200px;
            perspective: 1000px;
            margin-bottom: 20px;
            position: relative;
        }
        .card > div {
            width: 100%;
            height: 100%;
            position: absolute;
            backface-visibility: hidden;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            border-radius: 10px;
            background-color: #f6ebeb;
            color: #333;
            font-size: 1.2em;
            transition: transform 0.6s;
        }
        .front {
            background-color: #d39133;
        }
        .back {
            background-color: #f3a077;
            transform: rotateY(180deg);
        }
        #flashcard {
            transition: transform 0.6s;
            transform-style: preserve-3d;
        }
        #flashcard.flipped {
            transform: rotateY(180deg);
        }
        button {
            padding: 10px 20px;
            border: none;
            background-color: #0e1010;
            color: white;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            margin: 10px 0;
        }
        button:hover {
            background-color: #f32909;
        }
        form {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
        }
        label, input {
            width: 100%;
            margin: 5px 0;
        }
        input {
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        #score {
            margin: 40px 0;
            font-size: 1.2em;
        }
        .hidden {
            display: none;
        }
        #endScreen {
            display: none;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5); /* Semi-transparent background */
            justify-content: center;
            align-items: center;
            z-index: 1000; /* Ensure it appears on top */
        }
        #endScreen.active {
            display: flex;
        }
        #endScreenContent {
            background-color: #ffffff;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Flashcard Quiz App</h1>
        <div class="content">
            <div class="flashcard-container">
                <div class="card" id="flashcard">
                    <div class="front" id="question">Question goes here</div>
                    <div class="back" id="answer">Answer goes here</div>
                </div>
                <div id="score"></div>
                <div id="congratsMessage" class="hidden">Congratulations! You answered all questions correctly!</div>
            </div>
            <div class="form-container">
                <h2>Add New Flashcard</h2>
                <form id="flashcardForm">
                    <label for="newQuestion">Question:</label>
                    <input type="text" id="newQuestion" required>
                    <label for="newAnswer">Answer:</label>
                    <input type="text" id="newAnswer" required>
                    <button type="submit">Add Flashcard</button>
                </form>
            </div>
        </div>
    </div>

    <div id="endScreen">
        <div id="endScreenContent">
            <h1>Quiz Results</h1>
            <div id="finalScore"></div>
            <div id="congratsMessageEnd" class="hidden">Congratulations! You answered all questions correctly!</div>
            <button id="newGameBtn">New Game</button>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>
style.css -
const flashcard = document.getElementById('flashcard');
const questionEl = document.getElementById('question');
const answerEl = document.getElementById('answer');
const scoreEl = document.getElementById('score');
const flashcardForm = document.getElementById('flashcardForm');
const newQuestion = document.getElementById('newQuestion');
const newAnswer = document.getElementById('newAnswer');
const congratsMessage = document.getElementById('congratsMessage');
const endScreen = document.getElementById('endScreen');
const finalScore = document.getElementById('finalScore');
const congratsMessageEnd = document.getElementById('congratsMessageEnd');
const newGameBtn = document.getElementById('newGameBtn');

let flashcards = [];
let currentIndex = 0;
let score = 0;
let totalAttempts = 0;

function loadFlashcard() {
    if (flashcards.length === 0) {
        questionEl.textContent = 'No flashcards available. Add some!';
        answerEl.textContent = '';
        return;
    }
    const flashcardData = flashcards[currentIndex];
    questionEl.textContent = flashcardData.question;
    answerEl.textContent = flashcardData.answer;
    flashcard.classList.remove('flipped');
    congratsMessage.classList.add('hidden');
}

flashcard.addEventListener('click', () => {
    if (flashcards.length === 0) return;
    
    flashcard.classList.toggle('flipped');
    if (flashcard.classList.contains('flipped')) {
        totalAttempts++;
        const userAnswer = prompt('What is your answer?');
        if (answerEl.textContent.trim().toLowerCase() === userAnswer.trim().toLowerCase()) {
            score++;
        }
        updateScore();
        // Automatically move to the next flashcard
        currentIndex = (currentIndex + 1) % flashcards.length;
        setTimeout(loadFlashcard, 1000); // Add a slight delay before loading the next card
        
        // Check if all questions have been answered
        if (currentIndex === 0) {
            showEndScreen();
        }
    }
});

flashcardForm.addEventListener('submit', (e) => {
    e.preventDefault();
    const newFlashcard = {
        question: newQuestion.value,
        answer: newAnswer.value,
    };
    flashcards.push(newFlashcard);
    newQuestion.value = '';
    newAnswer.value = '';
    if (flashcards.length === 1) loadFlashcard();
});

newGameBtn.addEventListener('click', () => {
    resetGame();
});

function resetGame() {
    flashcards = [];
    currentIndex = 0;
    score = 0;
    totalAttempts = 0;
    loadFlashcard();
    updateScore();
    hideEndScreen();
}

function updateScore() {
    scoreEl.textContent = `Score: ${score} / ${totalAttempts}`;
}

function showEndScreen() {
    endScreen.classList.add('active');
    finalScore.textContent = `Final Score: ${score} / ${totalAttempts}`;
    if (score === flashcards.length && totalAttempts === flashcards.length) {
        congratsMessageEnd.classList.remove('hidden');
    }
}

function hideEndScreen() {
    endScreen.classList.remove('active');
}
