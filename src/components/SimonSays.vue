<template>
    <div>
        <h1>{{ msg }}</h1>

        <div class="wrapper">
            <div class="game-board">
                <div class="simon" v-bind:class="{cursor: hasGameStarted}">
                    <ul>
                        <li class="tile red" v-bind:class="{active: isActive(1)}"
                            @click="keepPlaying(1)"></li>
                        <li class="tile blue" ref="myBtn2" v-bind:class="{active: isActive(2)}"
                            @click="keepPlaying(2)"></li>
                        <li class="tile yellow" ref="myBtn3" v-bind:class="{active: isActive(3)}"
                            @click="keepPlaying(3)"></li>
                        <li class="tile green" ref="myBtn4" v-bind:class="{active: isActive(4)}"
                            @click="keepPlaying(4)"></li>
                    </ul>
                </div>
            </div>

            <div class="game-info">
                <h2>Раунд: <span>{{round}}</span></h2>
                <button @click="init()">Старт</button>
            </div>
            <div class="game-options">
                <h2>Уровень игры:</h2>
                <div v-for="level in levels" :key="level.val">
                    <input type="radio" :id="level.val" :value="level.val" v-model="selectedLevel">
                    <label :for="level.val">{{level.name}}</label>
                    <br>
                </div>
            </div>

        </div>

    </div>
</template>

<script>
    import sounds from "../sounds/sounds";

    export default {
        name: "SimonSays",
        props: {
            msg: String
        },
        data() {
            return {
                round: 0,
                selectedBtn: null,
                sequence: [],
                reserveSequence: [],
                hasGameStarted: false,
                player: new Audio(),
                sounds: sounds,
                selectedLevel: 1000,
                levels: [
                    {name: 'Легкий', val: 1500},
                    {name: 'Средний', val: 1000},
                    {name: 'Сложный', val: 400}
                ]
            }
        },
        methods: {
            init() {
                this.refreshSequence();
                this.round = 1;
                this.hasGameStarted = true;
                const randomNumber = this.getRandomNumber();
                this.sequence.push(randomNumber);
                this.reserveSequence = this.sequence;
                this.enlightenButton(randomNumber);
                this.playSound(randomNumber);
            },

            keepPlaying(num) {
                if (this.hasGameStarted && this.reserveSequence.length > 0) {
                    this.playSound(num);
                    this.enlightenButton(num);

                    if (this.reserveSequence[0] === num) {
                        this.reserveSequence = this.reserveSequence.slice(1);
                        if (this.reserveSequence.length === 0) {
                            setTimeout(() => {
                                this.newRound();
                            }, 800);

                        }
                    } else {
                        this.endGame();
                    }
                }
            },

            newRound() {
                this.round++;
                const randomNumber = this.getRandomNumber();
                this.sequence.push(randomNumber);
                this.reserveSequence = this.sequence;
                this.enlightenBtns();
            },

            enlightenBtns() {
                /*for (let i = 0; i < this.sequence.length; i++) {
                    this.playSound(this.sequence[i]);
                    this.enlightenButton(this.sequence[i]);
                    await this.sleep(this.selectedLevel);
                }*/
                var i = 0;
                let self = this;
                var interval = setInterval(function() {
                    self.playSound(self.sequence[i]);
                    self.enlightenButton(self.sequence[i]);

                    i++;
                    if (i >= self.sequence.length) {
                        clearInterval(interval);
                    }
                }, this.selectedLevel);

            },
            sleep(duration) {
                return new Promise(resolve => {
                    setTimeout(() => {
                        resolve()
                    }, duration)
                })
            },

            playSound(num) {
                this.player.src = this.sounds[num - 1].src;
                this.player.play();
            },
            endGame() {
                this.hasGameStarted = false;
                this.refreshSequence();
                this.round = 0;
            },

            refreshSequence() {
                this.sequence = [];
                this.reserveSequence = [];
            },

            enlightenButton(num) {
                this.selectedBtn = num;
                this.refreshTileBtn();
            },

            isActive(num) {
                return this.selectedBtn === num;
            },

            refreshTileBtn() {
                setTimeout(() => {
                    this.selectedBtn = null
                }, 230);
            },
            getRandomNumber() {
                return Math.floor(Math.random() * 4) + 1;
            }
        },
    }
</script>

<style scoped>


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

    .wrapper {
        width: 540px;
        margin: 0 auto;
    }

    .simon {
        background: #fff;
        position: relative;
        float: left;
        margin-right: 3em;
        width: 302px;
        height: 295px;
        /*-webkit-border-radius: 150px 150px 150px 150px;
        border-radius: 150px 150px 150px 150px;*/
        -moz-box-shadow: 2px 1px 12px #aaa;
        -webkit-box-shadow: 2px 1px 12px #aaa;
        -o-box-shadow: 2px 1px 12px #aaa;
        box-shadow: 2px 1px 12px #aaa;
    }

    .cursor {
        cursor: pointer;
    }

    .tile {
        opacity: 0.6;
        -webkit-transition: opacity 250ms ease;
        -moz-transition: opacity 250ms ease;
        -ms-transition: opacity 250ms ease;
        -o-transition: opacity 250ms ease;
        transition: opacity 250ms ease;
    }

    .active {
        opacity: 1 !important;
    }

    /*.tile:active {
        opacity: 1 !important;
    }*/

    .red, .blue, .yellow, .green {
        height: 290px;
        /*-webkit-border-radius: 150px 150px 150px 150px;
        border-radius: 150px 150px 150px 150px;*/
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

    .game-info button {
        width: 5em;
        box-sizing: border-box;
        font-size: 1.4em;
        -webkit-border-radius: 10px 10px 10px 10px;
        border-radius: 10px 10px 10px 10px;
        background: #6DABE8;
        border: none;
        padding: 0.3em 0.6em;
    }

    .game-info button:hover {
        background: #78BCFF;
    }

    .game-options h2 {
        margin-top: 30px;
        margin-bottom: 0;
    }

    .game-options input[type="radio"] {
        margin-right: 10px;
    }


</style>
