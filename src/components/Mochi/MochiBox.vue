<template>
    <div class="MochiBox /" :class="[shiba, { pops: pops }]" @click="randomize" tabindex="0">
        <mochi-shiba
                :size="size"
                :mood="mood"
                :leftEye="leftEye"
                :rightEye="rightEye"
                :leftEar="leftEar"
                :rightEar="rightEar"
                :blush="blush"
        />
        <div class="MochiContent">
            <slot></slot>
        </div>
        <mochi-paws :size="size" />
    </div>
</template>

<script>
    import MochiShiba from '@/components/Mochi/MochiShiba.vue'
    import MochiPaws from '@/components/Mochi/MochiPaws.vue'
    export default {
        name: "MochiBox",
        components:{
            MochiShiba,
            MochiPaws
        },
        data(){
            return{
                pops:this.pop
            }
        },
        props: {
            shiba: { type: String, default: "okaka" },
            size: { type: String, default: "medium" },
            mood: { type: String, default: "" },
            leftEye: { type: String, default: "open" },
            rightEye: { type: String, default: "open" },
            leftEar: { type: String, default: "up" },
            rightEar: { type: String, default: "flat" },
            blush: { type: Boolean, default: false },
            pop: { type: Boolean, default: true }
        },
        mounted() {
            let _ = this;
            let time = 3000 + Math.random() * 2000;
            setTimeout(()=>{
                _.pops = false;
            },time);
        },
        methods: {
            randomize() {
                if ( this.canRandom ) {
                    this.shiba = ["ume", "sesame", "tuna", "okaka", "anko", "kinako", "sakura", "monaka"].random();
                    this.mood = ["", "happy", "content", "excited", "cheeky", "drool", "cute", "gleam"].random();
                    this.leftEye = ["open", "wink", "shy", "laugh"].random();
                    this.rightEye = ["open", "wink", "shy", "laugh"].random();
                    this.leftEar = ["up", "down", "flat", "middle"].random();
                    this.rightEar = ["up", "down", "flat", "middle"].random();
                    this.blush = [true, false].random();
                }
            }
        },
        created() {
            if ( this.shiba === "random" ) {
                this.canRandom = true;
                this.randomize();
            }
        }
    }
</script>

<style scoped>
    .MochiShiba{
        left: 22%;
    }
    .MochiPaws{
        left: 22%;
    }
</style>
