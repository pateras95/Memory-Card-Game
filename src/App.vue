<template>
<div id="app">
    <div v-for="(card, index) in cards" :key="index" :class="[{'card-flip-down': card.down && !card.matched, 'card-flip-up': !card.down, 'card-match': card.matched}, ' card']" v-on:click="handleClick(card)">
        <i :class="'fas ' + card.icon"></i>
    </div>
</div>
</template>

<script>
const icons = [
    "fa-kiwi-bird",
    "fa-chess",
    "fa-frog",
    "fa-camera-retro",
    "fa-plug",
    "fa-anchor",
    "fa-birthday-cake",
    "fa-cube",
    "fa-dice",
    "fa-bug",
    "fa-cut",
    "fa-gem"
];

const checkMatch = icons => {
    if (icons[0] === icons[1]) {
        console.log("it's a match");
        return true;
    }
};

import _ from "lodash";
import "@fortawesome/fontawesome-free/css/all.css";
import "@fortawesome/fontawesome-free/js/all.js";

export default {
    name: "App",
    data() {
        return {
            cards: _.range(0, icons.length * 2),
            runing: false
        };
    },
    methods: {
        cardsShuffle() {
            this.cards.forEach((card, index) => {
                this.cards[index] = {
                    icon: "",
                    down: true,
                    matched: false
                };
            });
            // input every icon two times
            icons.forEach((icon, index) => {
                this.cards[index].icon = icon;
                this.cards[index + icons.length].icon = icon;
            });
            this.cards = _.shuffle(this.cards);
        },
        handleClick(cardClicked) {
            if (!this.runing) {
                // turn card up
                if (!cardClicked.matched && this.cardCount.cardsUp < 2) {
                    cardClicked.down = false;
                }
                // when two cards are up, check if they match or turn them down
                if (this.cardCount.cardsUp === 2) {
                    this.runing = true;
                    setTimeout(() => {
                        let match = checkMatch(this.cardCount.icons);
                        this.cards.forEach((card) => {
                            if (match && !card.down && !card.matched) {
                                card.matched = true;
                            } else {
                                card.down = true;
                            }
                        });
                        this.runing = false;
                    }, 1500);
                }
            }
        }
    },
    mounted() {
        this.cardsShuffle();
    },
    computed: {
        // make a count of cards up and cards matched, keep icons of cards to check in array
        cardCount: function () {
            let cardUpCount = 0;
            let cardMatchedCount = 0;
            let icons = [];
            this.cards.forEach(card => {
                if (!card.down && !card.matched) {
                    cardUpCount++;
                    icons.push(card.icon);
                }
                if (card.matched) {
                    cardMatchedCount++;
                }
            });
            return {
                cardsUp: cardUpCount,
                cardsMatched: cardMatchedCount,
                icons: icons
            };
        }
    }
};
</script>

<style scoped>
body {
    background: oldlace;
    padding: 20px;
    font-family: Helvetica, Arial, Sans-Serif;
}

svg {
    font-size: 30px;
}

#app {
    display: grid;
    grid-template-columns: repeat(auto-fit, 100px);
    grid-auto-rows: 100px;
    grid-gap: 15px;
    justify-content: center;
    perspective: 800px;
    max-width: 720px;
    margin: 0 auto;
}

@keyframes flipDown {
    0% {
        background: #fff;
        transform: rotateY(0deg);
        box-shadow: 5px 5px 0 0 #333;
    }

    100% {
        background: rgb(115, 132, 127);
        transform: rotateY(180deg);
        box-shadow: -5px 5px 0 0 #333;
    }
}

@keyframes flipUp {
    0% {
        background: rgb(115, 132, 127);
        transform: rotateY(180deg);
        box-shadow: -5px 5px 0 0 #333;
    }

    100% {
        background: #fff;
        transform: rotateY(0deg);
        box-shadow: 5px 5px 0 0 #333;
    }
}

@keyframes matching {
    0% {
        background: #fff;
    }

    100% {
        background: lightpink;
    }
}

.card-flip-up {
    background: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 5px;
    box-shadow: 5px 5px 0 0 #333;
    cursor: pointer;
    animation: flipUp 0.5s forwards;
}

.card-flip-down {
    background: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 5px;
    box-shadow: 5px 5px 0 0 #333;
    cursor: pointer;
    animation: flipDown 0.5s forwards;
}

.card-flip-down svg{
  opacity:0;
}


.card-match {
    background: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 5px;
    box-shadow: 5px 5px 0 0 #333;
    cursor: pointer;
    animation: matching 0.5s forwards;
}
</style>
