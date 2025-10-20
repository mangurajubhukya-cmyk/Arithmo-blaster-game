# Arithmo-blaster-game
To improve the thinking techniques in brain 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Arithmo-Blaster - Decimal Edition</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap" rel="stylesheet">
    <style>
        /* Custom styles for an arcade/sci-fi feel */
        :root {
            --primary-color: #4ade80; /* Neon Green */
            --secondary-color: #3b82f6; /* Blue */
            --danger-color: #ef4444; /* Red */
        }

        body {
            font-family: 'Orbitron', sans-serif;
            background-color: #0d1117; /* Dark space background */
            color: var(--primary-color);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
        }

        /* Game Area styling */
        #game-area {
            position: relative;
            width: 100%;
            max-width: 500px;
            height: 650px;
            background: rgba(1, 1, 1, 0.4);
            border: 4px solid var(--primary-color);
            box-shadow: 0 0 20px var(--primary-color);
            overflow: hidden;
            border-radius: 16px;
        }

        /* Falling problem styling */
        .falling-problem {
            position: absolute;
            top: 0;
            left: 50%;
            transform: translateX(-50%);
            padding: 1rem 1.5rem;
            background: var(--secondary-color);
            color: #1f2937;
            border: 2px solid #fff;
            border-radius: 8px;
            font-size: 1.5rem;
            text-align: center;
            box-shadow: 0 0 10px rgba(59, 130, 246, 0.8);
            z-index: 10;
            user-select: none;
            cursor: pointer;
        }

        /* Answer Button Styling */
        .answer-button {
            transition: all 0.1s ease;
            cursor: pointer;
            border: 3px solid var(--primary-color);
            background-color: rgba(74, 222, 128, 0.2);
            box-shadow: 0 0 8px var(--primary-color), 0 4px 0 var(--primary-color);
            font-size: 1.25rem;
            font-weight: bold;
        }

        .answer-button:hover, .answer-button:focus {
            background-color: rgba(74, 222, 128, 0.5);
            transform: translateY(-2px);
            box-shadow: 0 0 15px var(--primary-color), 0 6px 0 var(--primary-color);
        }

        .answer-button:active {
            transform: translateY(2px);
            box-shadow: 0 0 5px var(--primary-color), 0 2px 0 var(--primary-color);
        }

        /* Game Over/Start Modal */
        #modal {
            background: rgba(0, 0, 0, 0.9);
            z-index: 50;
        }

        .modal-content {
            background: #1f2937;
            border: 5px solid var(--danger-color);
            box-shadow: 0 0 25px var(--danger-color);
        }
        
        /* Difficulty Button Styling (Used for all three difficulty buttons) */
        .difficulty-button {
            background-color: var(--primary-color);
            color: #1f2937; /* Dark text on neon button */
            transition: transform 0.1s ease;
            box-shadow: 0 4px 0 rgba(0, 0, 0, 0.5);
        }

        .difficulty-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 0 rgba(0, 0, 0, 0.5);
        }
        
        .difficulty-button:active {
            transform: translateY(2px);
            box-shadow: 0 2px 0 rgba(0, 0, 0, 0.5);
        }

        /* Mobile specific adjustments */
        @media (max-width: 640px) {
            #game-area {
                height: 80vh; /* Use relative height on mobile */
                max-width: 95vw;
            }
            .answer-button {
                font-size: 1rem;
                padding: 0.75rem;
            }
            .falling-problem {
                font-size: 1.2rem;
                padding: 0.75rem 1rem;
            }
        }
    </style>
</head>
<body class="p-4">

    <div id="app" class="w-full max-w-lg flex flex-col items-center">
        <!-- Header: Score and Lives -->
        <div class="w-full flex justify-between p-2 mb-4 text-2xl font-bold bg-gray-800 rounded-lg shadow-lg">
            <div class="text-white">Score: <span id="score">0</span></div>
            <div class="text-danger-color">Lives: <span id="lives">3</span></div>
        </div>

        <!-- Main Game Area -->
        <div id="game-area">
            <!-- Falling problems will be dynamically added here -->
            <div id="problem-container"></div>
            
            <!-- Base Line / Danger Zone -->
            <div id="base-line" class="absolute bottom-0 left-0 w-full h-2 bg-danger-color opacity-70"></div>
        </div>

        <!-- Answer Buttons -->
        <div id="answer-container" class="w-full grid grid-cols-2 gap-3 mt-4">
            <button class="answer-button rounded-xl p-3" data-index="0"></button>
            <button class="answer-button rounded-xl p-3" data-index="1"></button>
            <button class="answer-button rounded-xl p-3" data-index="2"></button>
            <button class="answer-button rounded-xl p-3" data-index="3"></button>
        </div>

        <!-- Start/Game Over Modal -->
        <div id="modal" class="absolute inset-0 flex items-center justify-center p-4">
            <div class="modal-content p-8 rounded-2xl text-center shadow-2xl w-full max-w-xs">
                <h2 id="modal-title" class="text-3xl mb-4 text-white">Arithmo-Blaster</h2>
                <p id="modal-message" class="text-lg mb-6 text-gray-300">Blast the correct answer! Sharpen your mind with fast-paced **decimal addition**.</p>
                
                <!-- DIFFICULTY BUTTONS (Reverted to 3 buttons) -->
                <div id="difficulty-buttons-container" class="flex flex-col space-y-3 w-full">
                    <button class="difficulty-button px-6 py-3 rounded-xl font-bold text-xl" data-difficulty="easy" id="easy-start-button">
                        EASY <span class="text-sm font-normal">(Integers & 1 Decimal)</span>
                    </button>
                    <button class="difficulty-button px-6 py-3 rounded-xl font-bold text-xl" data-difficulty="medium" id="medium-start-button">
                        MEDIUM <span class="text-sm font-normal">(Mixed 1 & 2 Decimals)</span>
                    </button>
                    <button class="difficulty-button px-6 py-3 rounded-xl font-bold text-xl" data-difficulty="hard" id="hard-start-button">
                        HARD <span class="text-sm font-normal">(Heavy 2 Decimals)</span>
                    </button>
                </div>
            </div>
        </div>

    </div>

    <script>
        // Game Configuration
        const FALL_SPEED_INCREMENT = 0.05; // Speed increase every level
        const START_LIVES = 3;
        const BASE_SCORE_INCREMENT = 10;
        
        // Difficulty Presets
        const DIFFICULTY_SETTINGS = {
            easy: { startLevel: 1, baseSpeed: 0.4 },
            medium: { startLevel: 1, baseSpeed: 0.5 },
            hard: { startLevel: 3, baseSpeed: 0.7 },
        };

        // DOM Elements
        const scoreElement = document.getElementById('score');
        const livesElement = document.getElementById('lives');
        const gameArea = document.getElementById('game-area');
        const problemContainer = document.getElementById('problem-container');
        const answerButtons = Array.from(document.querySelectorAll('.answer-button'));
        const modal = document.getElementById('modal');
        const modalTitle = document.getElementById('modal-title');
        const modalMessage = document.getElementById('modal-message');
        const difficultyButtons = Array.from(document.querySelectorAll('.difficulty-button'));

        // Game State
        let score = 0;
        let lives = START_LIVES;
        let level = 1;
        let isGameRunning = false;
        let currentProblem = null;
        let animationFrameId = null;
        let lastTime = 0;
        let currentBaseSpeed = 0.5; 

        // --- Core Game Logic ---

        // Utility to shuffle an array
        const shuffleArray = (array) => {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
        };

        // Generates a random math problem involving floats
        const generateProblem = () => {
            let decimals;
            let maxInt;
            
            // Determine complexity based on current level
            if (level < 3) { 
                // Level 1-2 (Easy): Mostly integers and simple one decimal
                decimals = Math.random() < 0.6 ? 0 : 1;
                maxInt = 20;
            } else if (level < 5) { 
                // Level 3-4 (Medium): Mix of 1 and 2 decimals
                const r = Math.random();
                if (r < 0.3) { decimals = 0; } // 30% integers
                else if (r < 0.7) { decimals = 1; } // 40% one decimal
                else { decimals = 2; } // 30% two decimals
                maxInt = 50;
            } else { 
                // Level 5+ (Hard): Mostly 2 decimals
                decimals = Math.random() < 0.4 ? 1 : 2;
                maxInt = 100;
            }

            // Helper to generate a number (integer or float)
            const generateNumber = (d) => {
                let num = Math.floor(Math.random() * maxInt) + 1;
                if (d > 0) {
                    // Generate a decimal string (e.g., '0.12')
                    let decimalPart = (Math.random()).toFixed(d);
                    // Add the integer part and convert back to float, rounded to d places
                    num += parseFloat(decimalPart); 
                }
                return parseFloat(num.toFixed(d)); // Ensure the final number is correctly rounded for display
            };

            let num1 = generateNumber(decimals);
            let num2 = generateNumber(decimals);
            
            // Calculate the correct answer (use high precision then round to 2 decimals max for consistency)
            let rawAnswer = num1 + num2;
            let correctAnswer = parseFloat(rawAnswer.toFixed(2)); 
            
            // Use the original (possibly slightly less rounded) display numbers
            let questionText = `${num1} + ${num2} = ?`;
            
            // Generate distractors (incorrect answers)
            const answers = [correctAnswer];
            let attempts = 0;
            while (answers.length < 4 && attempts < 100) {
                let distractor;
                
                // Generate a close distractor, focusing on errors in the last decimal place
                let offset = (Math.random() < 0.5 ? 1 : -1) * (Math.random() * 0.5 + 0.05); // Offset between 0.05 and 0.5
                distractor = parseFloat((correctAnswer + offset).toFixed(2));
                
                // Ensure distractor is unique, non-negative, and not too far off (e.g., less than 5 away)
                if (!answers.includes(distractor) && distractor >= 0 && Math.abs(distractor - correctAnswer) < 5) {
                    answers.push(distractor);
                }
                attempts++;
            }
            
            // Fallback: If not enough unique distractors, force creation
            while(answers.length < 4) {
                 answers.push(parseFloat((correctAnswer + 1 + answers.length).toFixed(2)));
            }

            shuffleArray(answers);

            // Ensure all answer strings are well-formatted (e.g., 5.0 instead of 5)
            const formattedAnswers = answers.map(a => {
                // Remove trailing zeros if they are after the decimal, unless it's a forced 0 (like 5.0)
                // This makes the display cleaner (e.g., 5.00 becomes 5, 5.50 becomes 5.5)
                let s = String(a);
                if (s.indexOf('.') > -1 && s.endsWith('0')) {
                    return parseFloat(s);
                }
                return a;
            });


            return {
                questionText,
                correctAnswer: correctAnswer,
                answers: formattedAnswers,
                element: null, 
                y: 0,
            };
        };

        // Renders a new problem to the game area and updates answer buttons
        const spawnProblem = () => {
            if (currentProblem && currentProblem.element) {
                currentProblem.element.remove();
            }

            currentProblem = generateProblem();

            // 1. Create the DOM element for the falling problem
            const el = document.createElement('div');
            el.className = 'falling-problem';
            el.textContent = currentProblem.questionText;
            
            // Randomize X position slightly to add variety
            const randomX = Math.floor(Math.random() * (gameArea.clientWidth - 150)) + 75; 
            el.style.left = `${randomX}px`;
            el.style.top = '0px';

            problemContainer.appendChild(el);
            currentProblem.element = el;

            // 2. Update answer buttons
            answerButtons.forEach((button, index) => {
                // Display the number, but store its value as a string for safety
                button.textContent = currentProblem.answers[index];
                button.value = currentProblem.answers[index];
            });
        };

        // Handles click/tap on an answer button
        const handleAnswerClick = (event) => {
            if (!isGameRunning || !currentProblem) return;

            // Convert the button value (which is a string) back to a float for comparison
            const selectedAnswer = parseFloat(event.currentTarget.value);

            // IMPORTANT: Compare floating-point numbers using an epsilon (small tolerance)
            const tolerance = 0.001; 
            const isCorrect = Math.abs(selectedAnswer - currentProblem.correctAnswer) < tolerance;

            if (isCorrect) {
                // Correct Answer!
                score += BASE_SCORE_INCREMENT * level;
                scoreElement.textContent = score;
                
                // Visual feedback (flash green)
                gameArea.style.borderColor = 'var(--primary-color)';
                gameArea.style.boxShadow = '0 0 30px var(--primary-color)';
                setTimeout(() => {
                    gameArea.style.boxShadow = '0 0 20px var(--primary-color)';
                }, 100);

                // Check for level up every 5 correct answers
                if (score > 0 && score % (5 * BASE_SCORE_INCREMENT * (level > 0 ? level : 1)) === 0) {
                    level++;
                }

                currentProblem.element.remove();
                spawnProblem();

            } else {
                // Incorrect Answer
                loseLife();
                // Visual feedback (flash red)
                gameArea.style.borderColor = 'var(--danger-color)';
                gameArea.style.boxShadow = '0 0 30px var(--danger-color)';
                setTimeout(() => {
                    gameArea.style.boxShadow = '0 0 20px var(--primary-color)';
                }, 100);
            }
        };

        // Deducts a life and checks for game over
        const loseLife = () => {
            lives--;
            livesElement.textContent = lives;

            if (lives <= 0) {
                gameOver();
            } else {
                 // On losing a life, replace the problem so player can try a new one
                 currentProblem.element.remove();
                 spawnProblem();
            }
        };

        // The main game loop for movement and collision detection
        const gameLoop = (currentTime) => {
            if (!isGameRunning) {
                cancelAnimationFrame(animationFrameId);
                return;
            }

            // Calculate delta time for smooth, frame-rate independent movement
            const deltaTime = currentTime - lastTime;
            lastTime = currentTime;

            if (currentProblem && currentProblem.element) {
                const speed = currentBaseSpeed + (FALL_SPEED_INCREMENT * (level - 1));
                const distanceToMove = speed * deltaTime * 0.06; // Scale down for pixels

                currentProblem.y += distanceToMove;
                currentProblem.element.style.top = `${currentProblem.y}px`;

                // Collision detection: check if problem hit the base line (bottom)
                const gameAreaHeight = gameArea.clientHeight;
                if (currentProblem.y + currentProblem.element.offsetHeight > gameAreaHeight) {
                    // Problem hit the base - lose a life
                    currentProblem.element.remove();
                    loseLife();
                    if (isGameRunning) {
                        spawnProblem(); // Spawn new problem immediately
                    }
                }
            }

            animationFrameId = requestAnimationFrame(gameLoop);
        };

        // --- Game State Management ---

        // Function to start the game based on the selected difficulty button
        const startGame = (selectedDifficulty) => {
            const settings = DIFFICULTY_SETTINGS[selectedDifficulty] || DIFFICULTY_SETTINGS.medium;

            // Apply Difficulty Settings to Game State
            score = 0;
            lives = START_LIVES;
            level = settings.startLevel;
            currentBaseSpeed = settings.baseSpeed; 

            scoreElement.textContent = score;
            livesElement.textContent = lives;
            modal.classList.add('hidden');
            isGameRunning = true;
            lastTime = performance.now(); 

            // Clear any existing problems
            problemContainer.innerHTML = '';
            
            spawnProblem();
            animationFrameId = requestAnimationFrame(gameLoop);
        };

        const gameOver = () => {
            isGameRunning = false;
            cancelAnimationFrame(animationFrameId);

            // Show Game Over Modal
            modalTitle.textContent = 'GAME OVER';
            modalTitle.classList.remove('text-white');
            modalTitle.classList.add('text-danger-color');
            
            modalMessage.innerHTML = `Your Final Score: <span class="text-white font-bold">${score}</span>.<br>You reached Level ${level}.<br>Select a level to play again.`;
            modal.classList.remove('hidden');
            
            // Clean up last falling problem
            if (currentProblem && currentProblem.element) {
                currentProblem.element.remove();
            }
            currentProblem = null;
        };
        
        // --- Initialization and Event Listeners ---

        // Attach event listeners to the new difficulty buttons
        difficultyButtons.forEach(button => {
            const difficulty = button.getAttribute('data-difficulty');
            
            // Handle both click and touch for mobile responsiveness
            const clickHandler = (event) => {
                event.preventDefault(); 
                startGame(difficulty);
            };

            button.addEventListener('click', clickHandler);
            button.addEventListener('touchstart', clickHandler, { passive: false });
        });


        // Listeners for answer buttons remain the same
        answerButtons.forEach(button => {
            button.addEventListener('click', handleAnswerClick);
            button.addEventListener('touchstart', (e) => {
                e.preventDefault(); 
                handleAnswerClick(e);
            }, { passive: false });
        });

        // Initial setup for the modal appearance
        window.onload = () => {
             // Ensure initial modal state is correct
             modalTitle.textContent = 'Arithmo-Blaster';
             modalTitle.classList.add('text-white');
             modalTitle.classList.remove('text-danger-color');
             modal.classList.remove('hidden');
        };

    </script>
</body>
</html>

