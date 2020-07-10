<template>
    <div ref="ball" class="loaders ball" :style="`top:${y}px;left:${x}px;`">
        {{zi}}
    </div>
</template>

<script>
    import {sleep} from "../common";

    export default {
        name: 'Ball',
        props: {
            x: Number,
            y: Number,
            mainColor: String,
            zi: String,
            ball: Object,
        },
        data() {
            return {
                animationMS: 50,
                min: 60,
                max: 80,
                step: 1,
                baseFontSize: 20,
            }
        },
        mounted() {
            this.animate();
        },
        methods: {
            async animate() {
                while (true) {
                    let ball = this.$refs.ball;
                    if (!ball) {
                        break;
                    }
                    let {width} = ball.getBoundingClientRect();
                    if (width >= this.max) {
                        this.step = -1;
                    }
                    if (width <= this.min) {
                        this.step = 1;
                    }

                    width = this.step > 0 ? Math.floor(width * 1.05) : Math.floor(width * 0.95);
                    ball.style.width = `${width}px`;
                    ball.style.height = `${width}px`;
                    ball.style.lineHeight = `${width}px`;
                    ball.style.fontSize = `${(this.max / width) * this.baseFontSize }px`;
                    if (this.ball.bounceColor) {
                        ball.style.backgroundColor = this.ball.bounceColor;
                        ball.style.boxShadow = `0 0 20px ${this.ball.bounceColor}`
                    } else {
                        ball.style.backgroundColor = this.mainColor;
                        ball.style.boxShadow = `0 0 10px ${this.mainColor}`
                    }

                    if (this.ball.bounceColor) {
                        await sleep(this.animationMS * 0.4);
                    } else {
                        await sleep(this.animationMS);
                    }
                }
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .ball {
        position: fixed;
        width: 50px;
        height: 50px;
        border-radius: 50%;
        transform: translate(-50%, -50%);
        text-align: center;
        line-height: 50px;
        color: white;
        font-weight: bold;
        font-family: "楷体";
    }

</style>
