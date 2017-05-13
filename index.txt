//Directional Bools
//var rightPressed = false;
//var leftPressed = false;
//var upPressed = false;
//var downPressed = false;

//Canvas
var canvas = document.getElementById('canvas');
var context = canvas.getContext('2d');

//Create Player
function Player(){
	this.rightPressed = false;
	this.leftPressed = false;
	this.upPressed = false;
	this.downPressed = false;
    this.name = undefined;
    this.special = false;
    this.x = 0;
    this.y = 0;
    this.width = 20;
    this.height = 40;
    this.color = undefined;
    this.score = 5;
    //this.controlled = false;
    this.direction = 0
    this.speed = 2;
    this.baseColor = undefined
	this.warHeadx = this.x;
	this.warHeady = this.y;
    
    this.drawRec = function drawRecs() {
        context.beginPath();
        if(this.direction === 0 || this.direction === 180) {
            this.height = 40;
            this.width = 20; 
        }
        else {
            this.height = 20;
            this.width = 40;
        }
        context.rect(this.x, this.y, this.width, this.height);
        context.fillStyle = this.color;
        context.fill();
        context.closePath();
        this.moveRecs();
    }
    this.moveRecs = function moveRecs() {
        if(this.x < 50) {
            this.x = 560;
        }
        else if(this.x > 650-90) {
            this.x = 51;
        }
        else if(this.y < 50) {
            this.y = 560;
        }
        else if(this.y > 650-90) {
            this.y = 51;
        }
        
        
        //if(this.controlled === true) {
            if(this.rightPressed) {
                this.direction = 90;
                this.x += this.speed;
				this.warHeadx = this.x + this.width;
				this.warHeady = this.y + (this.height/2);
            }
            else if(this.leftPressed) {
                this.direction = 270;
                this.x -= this.speed;
				this.warHeadx = this.x;
				this.warHeady = this.y + (this.height/2);
				
            }
            else if (this.upPressed) {
                this.direction = 0;
                this.y -= this.speed;
				this.warHeadx = this.x + (this.width/2);
				this.warHeady = this.y;
            }
            else if(this.downPressed) {
                this.direction = 180;
                this.y += this.speed;
				this.warHeadx = this.x + (this.width/2);
				this.warHeady = this.y + this.height;
            }
		//}
        var bool1 = collision(this, playerArray[0]);
        var bool2 = collision(this, playerArray[1]);
        var bool3 = collision(this, playerArray[2]);
        var bool4 = collision(this, playerArray[3]);
        if(bool1) {
            specificCollision(this, playerArray[0]);
        }
        if(bool2) {
            specificCollision(this, playerArray[1]);
        }
        if(bool3) {
            specificCollision(this, playerArray[2]);
        }
        if(bool4) {
            specificCollision(this, playerArray[3]);
        }
    }    
}

//Create Players
var playerArray = []
for(var i = 0; i < 4; i++) {
    playerArray[i] = new Player(false, 0, 0, 10, 20, undefined, 0, 0)
}
scoreCount();

//Detect some sort of collision
function collision(rec1, rec2) {
    if(rec1.name === rec2.name) {
        return false;
    }
    else {
		if(rec1.warHeadx > rec2.x && rec1.warHeadx < rec2.x + rec2.width && rec1.warHeady > rec2.y && rec1.warHeady < rec2.y + rec2.height) {
			return true;
		}
        else {
            return false;
        }
    }
}

function specificCollision(rec1, rec2) {
    var minX = Math.ceil(50);
    var maxX = Math.floor(601);
    var xPos = Math.floor(Math.random() * (maxX - minX)) + minX;
    
    var minY = Math.ceil(50);
    var maxY = Math.floor(601);
    var yPos = Math.floor(Math.random() * (maxX - minX)) + minX;
	
	rec2.x = xPos;
	rec2.y = yPos;
	//If Head-On, Apply Directly to the forehead
	if((rec1.direction === 0 && rec2.direction === 180) || (rec1.direction === 90 && rec2.direction === 270) || (rec1.direction === 180 && rec2.direction === 0) || (rec1.direction === 270 && rec2.direction === 90)) {
		var minX = Math.ceil(50);
		var maxX = Math.floor(601);
		var xPos = Math.floor(Math.random() * (maxX - minX)) + minX;
    
		var minY = Math.ceil(50);
		var maxY = Math.floor(601);
		var yPos = Math.floor(Math.random() * (maxX - minX)) + minX;
		rec1.x = xPos;
		rec1.y = yPos;
		updateScoresLoserLoser(rec1, rec2);
	}
	else {
		updateScoresWinnerLoser(rec1, rec2);
	}
	
    //if(rec1.direction == 0 && rec2.direction == 90) {
    //    updateScoresWinnerLoser(rec2, rec1);
    //    rec1.x = xPos;
    //    rec1.y = yPos;
    //}
    //else if(rec1.direction == 90 && rec2.direction == 180) {
    //    updateScoresWinnerLoser(rec1, rec2);
    //    rec2.x = xPos;
    //    rec2.y = yPos;
    //}
    //else if(rec1.direction == 180 && rec2.direction == 270) {
    //    updateScoresWinnerLoser(rec1, rec2);
    //    rec2.x = xPos;
    //    rec2.y = yPos;
    //}
}

function updateScoresWinnerLoser(winner, loser) {
    if(winner.special === true) {
        winner.score += 1;
        loser.score -= 2;
    }
    else if(winner.special === false && loser.special === true) {
        winner.score += 2;
        loser.score -= 1;
        loser.special = false;
        winner.special = true;
        winner.speed = 5;
        loser.speed = 3;
        winner.color = "black"
        loser.color = loser.baseColor;
    }
    else{
        winner.score += 1;
        loser.score -= 1;
    }
    scoreCount();
    if(winner.score >= 15) {
        theEnd();
    }
}

function updateScoresLoserLoser(loser1, loser2) {
    scoreCount();
}

function theEnd() {
    document.removeEventListener("keyup", keyUp, false);
    document.removeEventListener("keydown", keyDown, false);
    document.getElementById("done").style.display = "block";
	
	function sleep(ms) {
		return new Promise((resolve) => setTimeout(resolve, ms));
	}
	
	(async function neverGonnaGiveYouUp() {
		await sleep(5000);
		location.assign("https://www.youtube.com/watch?v=dQw4w9WgXcQ");
	})();
}
function scoreCount() {
    var scores = document.getElementsByClassName("scores");
    for(var x = 0; x < 4; x++) {
        scores[x].textContent = playerArray[x].score;
    }
}

//TEMPORARY
//playerArray[0].controlled = true;

//Hardcode Names
playerArray[0].name = "Player1";
playerArray[1].name = "Player2";
playerArray[2].name = "Player3";
playerArray[3].name = "Player4";

//Hardcode Colors for each player
playerArray[0].color = "red";
playerArray[1].color = "blue";
playerArray[2].color = "green";
playerArray[3].color = "orange";

playerArray[0].baseColor = "red";
playerArray[1].baseColor = "blue";
playerArray[2].baseColor = "green";
playerArray[3].baseColor = "orange";

//Hardcode initial player positions
playerArray[0].x = 70;
playerArray[0].y = 70;
playerArray[1].x = 560
playerArray[1].y = 70
playerArray[2].x = 70;
playerArray[2].y = 540;
playerArray[3].x = 560;
playerArray[3].y = 540;

//Moving
document.addEventListener("keydown", keyDown, false); document.addEventListener("keyup", keyUp, false);
startGame();
function startGame() {
    var x = determineSpecial();
    playerArray[x].special = true;
    playerArray[x].color = "black";
    playerArray[x].speed = 5;
    setInterval(draw, 10);

}

function keyDown(event) {
    
    //switch(event.keyCode) {
        if(event.keyCode ==39) playerArray[0].rightPressed = true;
		if(event.keyCode ==68) playerArray[1].rightPressed = true;
		if(event.keyCode ==102) playerArray[2].rightPressed = true;
		if(event.keyCode ==76) playerArray[3].rightPressed = true;
		
        if(event.keyCode == 37) playerArray[0].leftPressed = true;
		if(event.keyCode ==65) playerArray[1].leftPressed = true;
		if(event.keyCode ==100) playerArray[2].leftPressed = true;
		if(event.keyCode ==74) playerArray[3].leftPressed = true;
		
        if(event.keyCode == 38) playerArray[0].upPressed = true;
		if(event.keyCode ==87) playerArray[1].upPressed = true;
		if(event.keyCode ==104) playerArray[2].upPressed = true;
		if(event.keyCode ==73) playerArray[3].upPressed = true;
		
        if(event.keyCode == 40) playerArray[0].downPressed = true;
		if(event.keyCode ==83) playerArray[1].downPressed = true;
		if(event.keyCode ==101) playerArray[2].downPressed = true;
		if(event.keyCode ==75) playerArray[3].downPressed = true;
		
    //}
}
function keyUp(event) {
    //switch(event.keyCode) {
        if(event.keyCode == 39) playerArray[0].rightPressed = false;
		if(event.keyCode == 68) playerArray[1].rightPressed = false;
		if(event.keyCode ==102) playerArray[2].rightPressed = false;
		if(event.keyCode ==76) playerArray[3].rightPressed = false;
		
        if(event.keyCode == 37) playerArray[0].leftPressed  = false;
		if(event.keyCode == 65) playerArray[1].leftPressed = false;
		if(event.keyCode ==100) playerArray[2].leftPressed = false;
		if(event.keyCode ==74) playerArray[3].leftPressed = false;
		
        if(event.keyCode ==  38) playerArray[0].upPressed = false;
        if(event.keyCode == 87) playerArray[1].upPressed = false;
		if(event.keyCode ==104) playerArray[2].upPressed = false;
		if(event.keyCode ==73) playerArray[3].upPressed = false;
		
		if(event.keyCode == 40) playerArray[0].downPressed  = false;
		if(event.keyCode == 83) playerArray[1].downPressed = false;
		if(event.keyCode ==101) playerArray[2].downPressed = false;
		if(event.keyCode ==75) playerArray[3].downPressed = false;
	//}
}

//Generate Random Number between 0 and 3 to determine which player becomes the special
function determineSpecial() {
    var min = Math.ceil(0);
    var max = Math.floor(4);
    return Math.floor(Math.random() * (max - min)) + min;
}
//Create the canvas


// Call ONCE every four rectangles
function draw() {
    context.clearRect(0, 0, canvas.width, canvas.height);
    context.rect(50, 50, 550, 550);
    context.fillStyle = "white";
    context.fill();
    context.lineWidth = 8;
    //context.strokeStyle = "black";
    context.stroke();
    playerArray.forEach(function(player) {
        player.drawRec();
    })
}