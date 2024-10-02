// SVG elements
let riderSvg;
let board;
let cactusGroup;
let scoreText;
let isJumping = false;
let jumpHeight = 130;
let jumpSpeed = 5;
let images = [
    { src: "./img/obstacle1.png", widthFactor: 10 },  
    { src: "./img/obstacle2.png", widthFactor: 20 }, 
    { src: "./img/obstacle3.png", widthFactor: 30 }   
];

// rider properties
let rider = {
    x: 50,
    y: 280, 
    width: 88,
    height: 200,
    jumping: false
};



function initGame() {
    board = document.getElementById("board");
    riderGroup = document.createElementNS("http://www.w3.org/2000/svg", "g");
    board.appendChild(riderGroup);

    riderSvg = document.createElementNS("http://www.w3.org/2000/svg", "image");
    riderSvg.setAttribute("x", rider.x);
    riderSvg.setAttribute("y", rider.y);
    riderSvg.setAttribute("width", rider.width);
    riderSvg.setAttribute("height", rider.height);
    riderSvg.setAttribute("href", "./img/cycle.png");
    board.appendChild(riderSvg);

    document.addEventListener("keydown", function(event) {
        if (event.code === "Space") {
            jump();
        }
    });

    startGame();
}

// Start the game
function startGame() {
    gameOver = false;
    score = 0;
    updateScore();
    addRandomImage();
}

// Game over function
function gameOverFunc() {
    gameOver = true;
    updateScore();
    alert("Game Over! Your score is: " + score);
}

// Update the score
function updateScore() {
    if (!scoreText) {
        scoreText = document.createElementNS("http://www.w3.org/2000/svg", "text");
        scoreText.setAttribute("x", 700);
        scoreText.setAttribute("y", 30);
        scoreText.setAttribute("fill", "black");
        board.appendChild(scoreText);
    }
    scoreText.textContent = "Score: " + score;
}

// Rider jump function
function jump() {
    if (!isJumping) {
        isJumping = true;
        let startY = rider.y;
        let jumpInterval = setInterval(() => {
            rider.y -= jumpSpeed;
            if (rider.y <= startY - jumpHeight) {
                clearInterval(jumpInterval);
                let fallInterval = setInterval(() => {
                    rider.y += jumpSpeed;
                    if (rider.y >= startY) {
                        rider.y = startY;
                        isJumping = false;
                        clearInterval(fallInterval);
                    }
                    riderSvg.setAttribute("y", rider.y);
                }, 25);
            }
            riderSvg.setAttribute("y", rider.y);
        }, 25);
    }
}

// Check collision
function checkCollision(elem1, elem2) {
    let rect1 = elem1.getBoundingClientRect();
    let rect2 = elem2.getBoundingClientRect();

    let centerX1 = rect1.left + rect1.width / 2;
    let centerY1 = rect1.top + rect1.height / 2;
    let centerX2 = rect2.left + rect2.width / 2;
    let centerY2 = rect2.top + rect2.height / 2;

    let distanceX = Math.abs(centerX1 - centerX2);
    let distanceY = Math.abs(centerY1 - centerY2);

    return distanceX < 70 && distanceY < 70;
}


// Adding obstacles
function addRandomImage() {
    if (gameOver) return;

    let selectedImage = images[Math.floor(Math.random() * images.length)];

    let imageSvg = document.createElementNS("http://www.w3.org/2000/svg", "image");
    imageSvg.setAttribute("x", 800); 
    imageSvg.setAttribute("y", 380); 
    imageSvg.setAttribute("width", 20 * selectedImage.widthFactor); 
    imageSvg.setAttribute("height", 55); 
    imageSvg.setAttribute("href", selectedImage.src); 
    board.appendChild(imageSvg);
// moving obstacles
    function moveImage() {
        if (gameOver) {
            clearInterval(moveInterval);
            return;
        }

        let x = parseInt(imageSvg.getAttribute("x"));
        x -= 3; 
        imageSvg.setAttribute("x", x);

        if (checkCollision(riderSvg, imageSvg)) {
            gameOverFunc();
            clearInterval(moveInterval);
            return;
        }

        if (x + parseInt(imageSvg.getAttribute("width")) < 0) {
            
            board.removeChild(imageSvg);
            clearInterval(moveInterval);
        }
    }
    

    let moveInterval = setInterval(moveImage, 10); 
    score++; 
    updateScore(); 

    setTimeout(addRandomImage, 2000); 
}

initGame();
// animation for cycle for moving it up and down 
function animateCycle() {
    let startY = rider.y;
    let direction = 1;
    let amplitude = 25; 
    let speed = 0.4; 

    setInterval(() => {
        if (direction === 1 && rider.y < startY + amplitude) {
            rider.y += speed;
        } else if (direction === -1 && rider.y > startY - amplitude) {
            rider.y -= speed;
        } else {
            direction *= -1; 
        }

        riderSvg.setAttribute("y", rider.y);
    }, 25); 
}

animateCycle();
