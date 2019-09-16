<template>
 <v-app>
    <section class="row container">
        <div class="small-5 columns">
            <h1 class="text-center text-uppercase">Thor</h1>
            <img src="https://i.gifer.com/S5Qj.gif" alt="God of Thunder">
            <div class="healthbar">
                <div
                    v-ripple
                    class="healthbar text-center ma-0 elevation-20 headline"
                    style="background-color: green; margin: 0; color: white;"
                    :style="{width: playerHealth + '%', background: color}">
                    {{ playerHealth }}
                </div>
            </div>
        </div>

<div class="row">
<div class="small-12 columns">
            
    <section class="row controls" v-if="!gameIsRunning">
        <v-flex>
        <v-btn rounded color="success" @click="startGame" dark>START NEW GAME</v-btn>
        </v-flex>
    </section>

    <section class="row controls" v-else>
        <v-flex s12>
            <v-tooltip right>
                    <template v-slot:activator="{ on }">
                        <v-btn rounded color="error" @click="attack() + changeColor()" v-on="on" dark>ATTACK</v-btn>
                    </template>
                        <span>3-10 Damage</span>
                </v-tooltip><br>
                <v-tooltip right>
                    <template v-slot:activator="{ on }">
                        <v-btn rounded color="warning" @click="specialAttack() + changeColor()" v-on="on" dark>SPECIAL ATTACK</v-btn>
                    </template>
                        <span>10-20 Damage</span>
                </v-tooltip><br>
            <v-btn rounded color="success" @click="heal() + changeColor()" dark>HEAL</v-btn><br>
            <v-btn rounded color="alert" @click.stop="dialog = true" dark>GIVE UP</v-btn>
        </v-flex>

<!-- dialog box for giving up -->

        <v-dialog v-model="dialog" max-width="290">
            <v-card>
                <v-card-title class="headline">Give up to Thanos?</v-card-title>
                <v-card-text>
                Giving up to Thanos will result in half the people in the Universe disappearing.
                </v-card-text>
                <v-card-actions>
                <div class="flex-grow-1"></div>

                <v-btn color="green darken-1" text @click="dialog = false">Fight</v-btn>

                <v-btn color="green darken-1" text @click="giveUp()">Give Up</v-btn><!-- @click="giveUp" -->
                </v-card-actions>
            </v-card>
        </v-dialog>
    </section>

</div>
</div>
    <div class="small-5 columns">
        <h1 class="text-center text-uppercase">Thanos</h1>
        <div class="char">
            <img @click="overlay = !overlay" src="https://i.ibb.co/2qDq1j9/1234.gif" alt="Thanos">
        </div>
        
        <div class="healthbar">
            <div
                v-ripple
                class="healthbar text-center ma-0 elevation-20 headline"
                style="background-color: green; margin: 0; color: white;"
                :style="{width: monsterHealth + '%'}">
                {{ monsterHealth }}
            </div>
        </div>
    </div>
    </section>
    
    <!-- Lists out all the damage per turn when attack/special attack/heal is pressed -->

    <section class="row log container" v-if="turns.length > 0">
        <div class="small-12 columns">
            <ul>
                <li v-for="turn in turns"
                v-bind:key="turn.id"
                    :class="{'player-turn': turn.isPlayer, 'monster-turn': !turn.isPlayer}">
                    {{ turn.text }}
                </li>
            </ul>
        </div>
    </section>    
 </v-app>
</template>

<script>


export default {
  name: 'App',
  components: {
  },
   data: function() {
     return {
        absolute: true,
        overlay: false,
        dialog: false,
        playerHealth: 100,
        monsterHealth: 100,
        gameIsRunning: false,
        turns: [],
        clicked: [],
        selected: 0,
        color: 'green'
        }        
    },
    methods: {
        changeColor: function () {
            if (this.playerHealth <= 25) {
                this.color = 'red';
            }
            else {
                this.color = 'green';
            }
        },
        startGame: function () {
            this.gameIsRunning = true;
            this.dialog = false;
            this.playerHealth = 100;
            this.monsterHealth = 100;
            this.turns = [];
        },
        attack: function () {
            var damage = this.calculateDamage(3, 10);
            this.monsterHealth -= damage;
            this.turns.unshift({
                isPlayer: true,
                text: 'Player hits Monster for ' + damage
            });
            if (this.checkWin()) {
                return;
            }
            this.monsterAttacks();
        },
        specialAttack: function () {
                var damage = this.calculateDamage(10, 20);
                this.monsterHealth -= damage;
                this.turns.unshift({
                    isPlayer: true,
                    text: 'Player hits Monster hard for ' + damage
                });
                if (this.checkWin()) {
                    return;
                }
                    this.monsterAttacks();
        },
        heal: function () {
            if (this.playerHealth <= 90) {
                this.playerHealth += 10;
            } else {
                this.playerHealth = 100;
            }
            this.turns.unshift({
                isPlayer: true,
                text: 'Player heals for 10'
            });
            this.monsterAttacks();
        },
        giveUp: function () {
            this.gameIsRunning = false;
        },
        monsterAttacks: function() {
            var damage = this.calculateDamage(5, 12);
            this.playerHealth -= damage;
            this.checkWin();
            this.turns.unshift({
                isPlayer: false,
                text: 'Monster hits Player for ' + damage
            });
        },
        calculateDamage: function(min, max) {
            return Math.max(Math.floor(Math.random() * max) + 1, min);
        },
        checkWin: function() {
            if (this.monsterHealth <= 0) {
                if (confirm('You won! New Game?')) {
                    this.startGame();
                } else {
                    this.gameIsRunning = false;
                }
                return true;
            } else if (this.playerHealth <= 0) {
                if (confirm('You lost! New Game?')) {
                    this.startGame();
                } else {
                    this.gameIsRunning = false;
                }
                return true;
            }
            return false;
        }
    }
}
</script>

<style>

#app {
    font-family: 'Avenir', Helvetica, Arial, Helvetica, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    background-color: #424f56;
    margin-top: 60px;
    /* padding-left: 500px; */
}


h1 {color: white;}

html {
    background-color: #424f56;
}

.char {
    height: 300px;
}

.char img {
    padding-top: 70px;
}

.text-center {
    text-align: center;
}

.healthbar {
    width: 80%;
    height: 40px;
    background-color: #eee;
    margin: auto;
    transition: width 500ms;
}

.controls, .log {
    margin-top: 50px;
    /* text-align: right; */
}

.turn {
    margin-top: 0px;
    margin-bottom: 20px;
    font-weight: bold;
    font-size: 22px;
}

.log ul {
    list-style: none;
    font-weight: bold;
    text-transform: uppercase;
    margin: 0 500px;
}

.log ul li {
    margin: 5px;
}

.log ul .player-turn {
    color: blue;
    background-color: #e4e8ff;
}

.log ul .monster-turn {
    color: red;
    background-color: #ffc0c1;
}

button {
    font-size: 20px;
    background-color: #eee;
    padding: 12px;
    box-shadow: 0 1px 1px black;
    margin: 10px;
}
</style>