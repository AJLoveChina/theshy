<template>
    <div id="app" @click="click">

        <main>
            <div class="loaders">
                <div class="loader">
                    <div class="ball-pulse">
                        <div></div>
                        <div></div>
                        <div></div>
                    </div>
                </div>
            </div>
        </main>

        <div ref="countDownContainer" id="count-down-container" v-show="bShowCountDown">
            {{countDownIndex}}
        </div>

        <template v-for="ball in balls">
            <Ball :color="ball.color || 'orange'" :x="ball.x" :y="ball.y"></Ball>
        </template>

        <template v-for="(ball, index) in ballsV2">
            <Ball2 :ref="'ball-' + index" :x="ball.x" :y="ball.y" :main-color="ball.color" :zi='ball.zi'
                   :ball="ball"></Ball2>
        </template>
    </div>
</template>

<script>
    import Ball from './components/Ball';
    import Ball2 from './components/Ball2';
    import {sleep} from "./common";

    export default {
        name: 'app',
        components: {
            Ball,
            Ball2,
        },
        data() {
            return {
                balls: [],
                ballsV2: [],
                ziList: ['龍', '钱', '帅', '鼎', '雷', '火', '风', '雨', '侠', '虎', '羊', '狗', '猪'],
                usedZiList: [],
                colors: ['#4285f4', '#A7A7A7', '#fbbc05', '#769cdb', '#45ad61'],
                usedColors: [],
                bShowCountDown: false,
                bCountDownOver: null,
                countDownIndex: 5,
                countDownInterval: null,
                randomChooseIndex: 0,
                defaultBounceColor: '#F46445',
                maxSupportUsers: 12,

                changeMS: 1000,
                initChangeMS:1000,
                minChangeMS: 200,
                minChangeMSLastMS: 3000,
                changeMSX: 0,
                sleepMS: 40,
            }
        },
        created() {
        },
        mounted() {
            this.$refs.countDownContainer.style.lineHeight = `${window.innerHeight}px`;
        },
        watch: {
            countDownIndex(newVal, oldVal) {
                if (newVal <= 0) {
                    this.randomChoose();
                    this.modifyChangMS();
                }
            }
        },
        methods: {
            easeInOutCubic(x) {
                return x < 0.5 ? 4 * x * x * x : 1 - Math.pow(-2 * x + 2, 3) / 2;
            },
            modifyChangMS() {
                let x = 0;
                setInterval(() => {
                    if (x <= 3000) {
                        this.changeMS = - (this.initChangeMS - this.minChangeMS) / 3000 * x + this.initChangeMS;
                    } else if (x > 3000 && x < 6000) {

                    } else {
                        this.changeMS += 30
                    }
                }, 50);
            },
            addBall(x, y) {
                this.balls.push({
                    x,
                    y,
                    name: "x",
                    color: "#ea4436"
                })
            },
            randomColor() {
                let i = 0;
                let max = 50;
                while (true) {
                    let randomIndex = Math.floor(Math.random() * this.colors.length);
                    let chooseColor = this.colors[randomIndex];
                    if (this.usedColors.indexOf(chooseColor) === -1) {
                        this.usedColors.push(chooseColor);
                        return chooseColor;
                    }

                    if (i++ > max) {
                        return chooseColor;
                    }
                }
            },
            randomZI() {
                while (true) {
                    let randomIndex = Math.floor(Math.random() * this.ziList.length);
                    let zi = this.ziList[randomIndex];
                    if (this.usedZiList.indexOf(zi) === -1) {
                        this.usedZiList.push(zi);
                        return zi;
                    }
                }
            },
            addBallV2(x, y) {
                let color = this.randomColor();
                let zi = this.randomZI();
                this.ballsV2.push({
                    x,
                    y,
                    color,
                    zi,
                })
            },

            countDown() {
                if (this.countDownInterval) return;
                this.bShowCountDown = true;
                this.bCountDownOver = false;

                this.countDownInterval = setInterval(() => {
                    this.countDownIndex -= 1;
                    if (this.countDownIndex <= 0) {
                        clearInterval(this.countDownInterval);
                        this.bCountDownOver = true;
                        this.countDownInterval = null;
                        this.bShowCountDown = false;
                    }
                }, 1000);
            },
            click(ev) {
                if (this.ballsV2.length > this.maxSupportUsers) {
                    return;
                }
                if (this.bCountDownOver === true) return;

                let {clientX, clientY} = ev;
                // this.addBall(clientX, clientY);
                this.addBallV2(clientX, clientY);
                this.countDown();
            },
            async randomChoose() {
                while (true) {
                    let ball = this.ballsV2[this.randomChooseIndex];

                    ball.bounceColor = this.defaultBounceColor;
                    await sleep(this.changeMS);

                    this.randomChooseIndex = (this.randomChooseIndex + 1) % this.ballsV2.length;
                    ball.bounceColor = null;
                }
            },
            mousedown(ev) {
                console.log("", ev);
            },
            mouseup(ev) {
                console.log(ev);
            }
        }
    }
</script>

<style>
    * {
        box-sizing: border-box;
        padding: 0;
        margin: 0;
    }

    html, body {
        width: 100%;
        height: 100%;
        position: relative;
    }

    #app {
        width: 100%;
        height: 100%;
    }

    .loader {
        box-sizing: border-box;
        display: -ms-flexbox;
        display: flex;
    }

    #count-down-container {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        text-align: center;
        font-size: 180px;
        color: #f3f3f3;
    }

</style>
