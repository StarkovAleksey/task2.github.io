<template>
    <form novalidate>
        <div class="wrapper">
            <h1>Simon Says</h1>
            <div class="game-board">
                <div>
                    <audio id="audio-red" src="../assets/sounds/1.ogg"></audio>
                    <audio id="audio-blue" src="../assets/sounds/2.ogg"></audio>
                    <audio id="audio-yellow" src="../assets/sounds/3.ogg"></audio>
                    <audio id="audio-green" src="../assets/sounds/4.mp3"></audio>
                </div>
                <div class="simon">
                    <ul>
                        <li class="tile red" data-tile="1" id="red" @click="btnClicked('red', gameTypeSelected)"></li>
                        <li class="tile blue" data-tile="2" id="blue" @click="btnClicked('blue', gameTypeSelected)"></li>
                        <li class="tile yellow" data-tile="3" id="yellow" @click="btnClicked('yellow', gameTypeSelected)"></li>
                        <li class="tile green" data-tile="4" id="green" @click="btnClicked('green', gameTypeSelected)"></li>
                    </ul>
                </div>
            </div>
            <div class="game-info">
                <h2>Round: <span data-round="0">{{ roundCount }}</span></h2>
                <input id="startButton" type="button" @click="startGame()" value="Start">
                <p v-if="isGameLost">Sorry, you lost after {{ roundCount }} rounds!</p>
            </div>
            <div class="game-options">
                <h2>Game Options:</h2>
                <h3>Game type</h3>
                <div>
                    <input id="radioNormal"
                           type="radio"
                           name="choiceGameType"
                           @click="handleClick('Normal')"
                           value="Normal">Normal<br>
                    <input id="radioSoundOnly"
                           type="radio"
                           name="choiceGameType"
                           @click="handleClick('Sound-only')"
                           value="Sound-only">Sound-only<br>
                    <input id="radioLightOnly"
                           type="radio"
                           name="choiceGameType"
                           @click="handleClick('Light-only')"
                           value="Light-only">Light-only<br>
                </div>
                <h3>Game speed</h3>
                <div>
                    <input id="radioSpeed1500"
                           type="radio"
                           name="choiceGameSpeed"
                           @click="handleSpeed(1500)"
                           value=1500>1.5 sec.<br>
                    <input id="radioSpeed1000"
                           type="radio"
                           name="choiceGameSpeed"
                           @click="handleSpeed(1000)"
                           value=1000>1 sec.<br>
                    <input id="radioSpeed400"
                           type="radio"
                           name="choiceGameSpeed"
                           @click="handleSpeed(400)"
                           value=400>0.4 sec.<br>
                </div>
            </div>
            <footer>
            </footer>
        </div>
    </form>
</template>

<script>
    export default {
        name: "SimonTheGame",
        props: [],
        components: {},
        created() {
        },
        data() {
            return {
                gameTypes: [{name: 'Normal'}, {name: 'Sound-only'}, {name: 'Light-only'}, {name: 'Free-board'}],
                gameTypeSelected: 'Normal',
                exampleArray: [],
                isErrorRepeat: false,
                isGameStarted: false,
                isGameLost: false,
                isWaitClick: false,
                isClickNext: true,
                pointCount: 0,
                currentCount: 0,
                roundCount: 0,
                gameSpeed: 0,
                sectorColorArray: [
                    {color: 'red', audio: '../assets/sounds/1.ogg'},
                    {color: 'blue', audio: '../assets/sounds/2.ogg'},
                    {color: 'yellow', audio: '../assets/sounds/3.ogg'},
                    {color: 'green', audio: '../assets/sounds/4.mp3'}
                ],
            }
        },
        mounted() {
            document.getElementById("radioNormal").checked = true;
            this.gameTypeSelected='Normal';
            document.getElementById("radioSpeed1500").checked = true;
            this.gameSpeed=1500;
        },
        methods: {
            handleClick(myRadio) {
                this.gameTypeSelected = myRadio;
            },
            handleSpeed(speed) {
                this.gameSpeed = speed;
            },
            btnClicked(key) {
                if (this.gameTypeSelected === 'Sound-only' || this.gameTypeSelected === 'Normal') {
                    var audio = document.getElementById("audio-" + key);
                    audio.play();
                }
                if (this.gameTypeSelected === 'Light-only' || this.gameTypeSelected === 'Normal') {
                    document.getElementById(key).classList.add('lit');
                    setTimeout(() => {
                        document.getElementById(key).classList.remove('lit');
                        this.isClickNext = true;
                    }, 100);
                }

                if (this.isGameStarted && this.isWaitClick) {
                    if (key === this.exampleArray[this.currentCount - 1]) {
                        this.pointCount++;
                        if (this.currentCount === this.exampleArray.length) {
                            this.isWaitClick = false;
                            this.roundCount++;
                            this.addPoint();
                        } else if (this.currentCount < this.exampleArray.length) {
                            this.currentCount++;
                        }
                    } else {
                        this.isGameStarted = false;
                        this.isGameLost = true;
                        this.pointCount = 0;
                        this.exampleArray = [];
                    }
                }
            },
            startGame() {
                this.isGameStarted = false;
                this.pointCount = 0;
                this.isGameLost = false;
                this.roundCount = 0;
                this.exampleArray = [];
                this.addPoint();
            },
            addPoint() {
                const random = Math.floor(Math.random() * this.sectorColorArray.length);
                this.exampleArray.push(this.sectorColorArray[random].color);
                let clickBtn = {
                    clicker(keyBtn, gameType) {
                        if (gameType === 'Sound-only' || gameType === 'Normal') {
                            var audio = document.getElementById("audio-" + keyBtn);
                            audio.play();
                        }
                        if (gameType === 'Light-only' || gameType === 'Normal') {
                            document.getElementById(keyBtn).classList.add('lit');
                            setTimeout(() => {
                                document.getElementById(keyBtn).classList.remove('lit');
                                this.isClickNext = true;
                            }, 200);
                        }
                    },
                };

                function start(counter, array, clickFunction, gameType, speed) {
                    if (counter < array.length) {
                        setTimeout(function () {
                            clickFunction.clicker(array[counter], gameType);
                            counter++;
                            start(counter, array, clickFunction, gameType, speed);
                        }, speed);
                    }
                }

                start(0, this.exampleArray, clickBtn, this.gameTypeSelected, this.gameSpeed);

                this.currentCount = 1;
                this.isGameStarted = true;
                this.isWaitClick = true;
            },
        },
        computed: {}

    }
</script>

<style scoped>
    body {
        font-family: Arial, serif;
        color: #333;
        -webkit-user-select: none; /* Chrome/Safari */
        -moz-user-select: none; /* Firefox */
        -ms-user-select: none; /* IE10+ */
        -o-user-select: none;
        user-select: none;
    }

    h1 {
        margin: 1em 0 2em;
        text-align: center;
    }

    ul {
        list-style: none;
    }

    ul, li {
        padding: 0;
        margin: 0;
    }

    p[data-action="lose"] {
        display: none;
    }

    .active {
        opacity: 1 !important;
    }

    .clearfix {
        width: 100%;
        clear: both;
    }

    .wrapper {
        width: 540px;
        margin: 0 auto;
    }

    .container {
        width: 305px;
    }

    .simon {
        background: #fff;
        position: relative;
        float: left;
        margin-right: 3em;
        width: 302px;
        height: 295px;
        -webkit-border-radius: 150px 150px 150px 150px;
        border-radius: 150px 150px 150px 150px;
        -moz-box-shadow: 2px 1px 12px #aaa;
        -webkit-box-shadow: 2px 1px 12px #aaa;
        -o-box-shadow: 2px 1px 12px #aaa;
        box-shadow: 2px 1px 12px #aaa;
    }

    .tile {
        opacity: 0.6;
        -webkit-transition: opacity 250ms ease;
        -moz-transition: opacity 250ms ease;
        -ms-transition: opacity 250ms ease;
        -o-transition: opacity 250ms ease;
        transition: opacity 250ms ease;
    }

    .tile.lit {
        opacity: 1;
    }

    .red, .blue, .yellow, .green {
        height: 290px;
        -webkit-border-radius: 150px 150px 150px 150px;
        border-radius: 150px 150px 150px 150px;
        position: absolute;
        text-indent: 10000px;
    }

    .red:hover, .blue:hover, .yellow:hover, .green:hover {
        border: 2px solid black
    }

    .red {
        background: #FF5643;
        clip: rect(0px, 300px, 150px, 150px);
        width: 296px;
    }

    .blue {
        background: dodgerblue;
        clip: rect(0px, 150px, 150px, 0px);
        width: 300px;
    }

    .yellow {
        background: #FEEF33;
        clip: rect(150px, 150px, 300px, 0px);
        width: 300px;
    }

    .green {
        background: #BEDE15;
        clip: rect(150px, 300px, 300px, 150px);
        width: 296px;
    }

    .game-info {
        margin-top: 90px;
    }

    .game-info #startButton {
        width: 5em;
        box-sizing: border-box;
        font-size: 1.4em;
        -webkit-border-radius: 10px 10px 10px 10px;
        border-radius: 10px 10px 10px 10px;
        background: #6DABE8;
        border: none;
        padding: 0.3em 0.6em;
    }

    .game-info #startButton:hover {
        background: #78BCFF;
    }

    .game-options h2 {
        margin-top: 30px;
        margin-bottom: 0;
    }

    .game-options input[type="radio"] {
        margin-right: 10px;
    }

    .hoverable:hover {
        cursor: pointer;
    }

    footer {
        position: absolute;
        bottom: 20px;
        width: 540px;
        clear: both;
        text-align: center;
    }

    .game-options div label {
        float: left;
        margin-left: 2em;
    }

    .game-options div input {
        float: right;
    }

    .game-options {
        width: 200px;
        display: table-cell;
    }
</style>