<template>
    <div id="app" @click="click">
        <div id="desc" v-if="!bStarted">
            <div class="pp title">The Shy</div>
            <div class="pp">😄大家将一个手指按到屏幕上任何位置, 系统随机挑选一个shy shy的Ta, 随机决定是谁喝酒、取外卖、洗碗等等</div>

            <div class="nigeerhuo tab-icon">&#xe6f7;</div>
        </div>

        <div id="info">
            <a href="https://github.com/AJLoveChina/theshy" target="_blank">open source link here</a><br>
            <span class="nigeerhuo">&#xe8d2;</span>Idea by LW Mao、HYY && Developed by <a href="https://github.com/AJLoveChina" target="_blank">霸都丶傲天</a>
        </div>

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

        <button v-if="bEnd" id="reload" @click.stop="reload()">再来一次</button>
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
                bStarted: false,
                bShowCountDown: false,
                bCountDownOver: null,
                countDownIndexInit: 3,
                countDownIndex: 3,
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

                bEnd: false,
                intervalsWaitClear: [],
            }
        },
        created() {
        },
        mounted() {
            let style = document.createElement('style');
            style.innerHTML = `#count-down-container{line-height:${window.innerHeight}px;}`
            document.body.appendChild(style);
        },
        watch: {
            countDownIndex(newVal, oldVal) {
                if (newVal <= 0) {
                    this.randomChoose();
                    this.modifyChangMS();
                    this.finalSelect();
                }
            },
            bEnd(newVal, oldVal) {
                if (this.bEnd) {
                    this.theshy();
                }
            }
        },
        methods: {
            reload() {
                window.location.reload()
            },
            theshy() {
                let ball = this.ballsV2[this.randomChooseIndex];
                this.ballsV2 = [ball];
                this.bounceBall(ball, true);
            },
            finalSelect() {
                let len = this.ballsV2.length;
                setTimeout(() => {
                    this.bEnd = true;
                    this.clear();
                }, (len * 1.2 + len * Math.random()) * 1000);
            },
            clear() {
                this.intervalsWaitClear.forEach(i => clearInterval(i));
            },
            modifyChangMS() {
                let x = 0;
                let interval = setInterval(() => {
                    x+= 50;
                    if (x <= 3000) {
                        this.changeMS = - (this.initChangeMS - this.minChangeMS) / 3000 * x + this.initChangeMS;
                    } else if (x > 3000 && x < 6000) {

                    } else {
                        this.changeMS += 5 + Math.random() * 10;
                    }
                }, 50);
                this.intervalsWaitClear.push(interval);
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

                this.bStarted = true;
                this.countDownIndex = this.countDownIndexInit;

                let {clientX, clientY} = ev;
                // this.addBall(clientX, clientY);
                this.addBallV2(clientX, clientY);
                this.countDown();
            },
            bounceBall(ball, bBounce) {
                if (bBounce) {
                    ball.bounceColor = this.defaultBounceColor;
                } else {
                    ball.bounceColor = null;
                }
            },
            async randomChoose() {
                this.randomChooseIndex = Math.floor(Math.random() * this.ballsV2.length);
                while (true) {
                    if (this.bEnd) {
                        break;
                    }
                    let ball = this.ballsV2[this.randomChooseIndex];

                    this.bounceBall(ball, true);
                    console.log(`sleep ${this.changeMS}`);
                    await sleep(this.changeMS);

                    if (this.bEnd) {
                        break;
                    }

                    this.randomChooseIndex = (this.randomChooseIndex + 1) % this.ballsV2.length;
                    this.bounceBall(ball, false);
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
        color:#333;
    }

    html, body {
        width: 100%;
        height: 100%;
        position: fixed;
    }

    .tab-icon{
        font-size: 100px;
        color:#aaa;
        margin-top:100px;
    }
    #info {
        position: fixed;
        left:0;bottom:0;
        font-size: 12px;
        color:#aaa;
        padding:5px;
    }
    #info a{
        color:#aaa;
    }

    #desc{
        width:80%;
        margin:20% auto;
        text-align: center;
    }
    #desc .pp{
        margin: 10px;
        color:#999;
    }
    #desc .title {
        font-size:30px;
        font-weight: bold;
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

    #reload{
        width:150px;
        padding: 10px;
        background-color: #409eff;
        color:white;
        position: fixed;
        left:50%;
        bottom:20%;
        transform: translateX(-50%);
        text-align: center;
        font-weight: 500;
        border:1px solid #409eff;
        border-radius: 4px;
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
