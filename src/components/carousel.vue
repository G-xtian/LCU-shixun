<template>
    <div style="width:350px;height: 300px;" >
        <div  style="border:1px solid #000">
            <pic-zoom :url="path" :scale="3" style="width: 348px;height: 300px"></pic-zoom>
        </div>
        <div class="card-carousel-wrapper">
            <div class="card-carousel--nav__left" @click="moveCarousel(-1)" :disabled="atHeadOfList"></div>
            <div class="card-carousel">
                <div class="card-carousel--overflow-container">
                    <div class="card-carousel-cards" :style="{ transform: 'translateX' + '(' + currentOffset + 'px' + ')'}">
                        <div class="card-carousel--card" :class="pic==path? 'card-blue':''" v-for="(pic) in pics" :key="pic"><a  @click="click(pic)" @mouseover="click(pic)"><img style="width: 50px;height: 50px" :src='pic'></a>
                        </div>
                    </div>
                </div>
            </div>
            <div class="card-carousel--nav__right" @click="moveCarousel(1)" :disabled="atEndOfList"></div>
        </div>
    </div>
</template>

<script>
    import PicZoom from '@/components/PicZoom.vue'
    export default {
        name: "carousel",
        components: {
            PicZoom
        },
        props:{
            src:{
                type:String
            },
            pics:{
                type:Array
            }
        },
        data() {
            return {
                path:this.src,
                currentOffset: 0,
                windowSize: 3,
                paginationFactor: 220
            }
        },
        computed: {
            atEndOfList() {
                return this.currentOffset <= (this.paginationFactor * -1) * (this.pics.length - this.windowSize);
            },
            atHeadOfList() {
                return this.currentOffset === 0;
            },
        },
        methods: {
            click(pic){
                this.path= pic
            },
            moveCarousel(direction) {
                // Find a more elegant way to express the :style. consider using props to make it truly generic
                if (direction === 1 && !this.atEndOfList) {
                    this.currentOffset -= this.paginationFactor;
                } else if (direction === -1 && !this.atHeadOfList) {
                    this.currentOffset += this.paginationFactor;
                }
            },
        }
    }
</script>

<style scoped>
    body {
        background: #f8f8f8;
        color: #2c3e50;
        font-family: 'Source Sans Pro', sans-serif;
        min-height: 1000px;
        padding:0;
        margin:0;
    }

    .card-carousel-wrapper {

        height: 50px;
        display: flex;
        align-items: center;
        justify-content: center;
        margin: 20px 0 40px;
        color: #666a73;
    }

    .card-carousel {
        display: flex;
        justify-content: center;
        width: 260px;
    }
    .card-carousel--overflow-container {
        overflow: hidden;
    }
    .card-carousel--nav__left, .card-carousel--nav__right {
        display: inline-block;
        width: 15px;
        height: 15px;
        padding: 10px;
        box-sizing: border-box;
        border-top: 2px solid #42b883;
        border-right: 2px solid #42b883;
        cursor: pointer;
        margin: 0 10px;
        transition: transform 150ms linear;
    }
    .card-carousel--nav__left[disabled], .card-carousel--nav__right[disabled] {
        opacity: 0.2;
        border-color: black;
    }
    .card-carousel--nav__left {
        transform: rotate(-135deg);
    }
    .card-carousel--nav__left:active {
        transform: rotate(-135deg) scale(0.9);
    }
    .card-carousel--nav__right {
        transform: rotate(45deg);
    }
    .card-carousel--nav__right:active {
        transform: rotate(45deg) scale(0.9);
    }

    .card-carousel-cards {
        display: flex;
        transition: transform 150ms ease-out;
        transform: translatex(0px);
    }
    .card-blue{
        border: 2px solid deepskyblue;
    }

    .card-carousel-cards .card-carousel--card {

        margin: 0 10px;
        cursor: pointer;
        box-shadow: 0 4px 15px 0 rgba(40, 44, 53, 0.06), 0 2px 2px 0 rgba(40, 44, 53, 0.08);
        background-color: #fff;
        border-radius: 4px;
        z-index: 3;
    }
    .card-carousel-cards .card-carousel--card:first-child {
        margin-left: 0;
    }
    .card-carousel-cards .card-carousel--card:last-child {
        margin-right: 0;
    }
    .card-carousel-cards .card-carousel--card img {
        vertical-align: bottom;
        transition: opacity 150ms linear;
        user-select: none;
    }
    .card-carousel-cards .card-carousel--card img:hover {
        opacity: 0.5;
    }
    .card-carousel-cards .card-carousel--card--footer {
        border-top: 0;
        padding: 7px 15px;
    }
    .card-carousel-cards .card-carousel--card--footer p {
        padding: 3px 0;
        margin: 0;
        margin-bottom: 2px;
        font-size: 19px;
        font-weight: 500;
        color: #2c3e50;
        user-select: none;
    }
    .card-carousel-cards .card-carousel--card--footer p:nth-of-type(2) {
        font-size: 12px;
        font-weight: 300;
        padding: 6px;
        background: rgba(40, 44, 53, 0.06);
        display: inline-block;
        position: relative;
        margin-left: 4px;
        color: #666a73;
    }
    .card-carousel-cards .card-carousel--card--footer p:nth-of-type(2):before {
        content: "";
        float: left;
        position: absolute;
        top: 0;
        left: -12px;
        width: 0;
        height: 0;
        border-color: transparent rgba(40, 44, 53, 0.06) transparent transparent;
        border-style: solid;
        border-width: 12px 12px 12px 0;
    }
    .card-carousel-cards .card-carousel--card--footer p:nth-of-type(2):after {
        content: "";
        position: absolute;
        top: 10px;
        left: -1px;
        float: left;
        width: 4px;
        height: 4px;
        border-radius: 2px;
        background: white;
        box-shadow: -0px -0px 0px #004977;
    }

    h1 {
        font-size: 3.6em;
        font-weight: 100;
        text-align: center;
        margin-bottom: 0;
        color: #42b883;
    }

</style>
