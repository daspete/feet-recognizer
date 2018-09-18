<template>
    <div class="recognizer">
        <!-- <img ref="testimage" src="/assets/images/test.jpg" alt=""> -->
        <video id="testvideo" width="800" height="600" ref="testvideo" autoplay loop muted></video>

        <div class="objects">
            <div class="object" v-if="legs.left" :style="GetObjectStyle(legs.left, 'right.png')"></div>
            <div class="object" v-if="legs.right" :style="GetObjectStyle(legs.right, 'left.png')"></div>
        </div>
    </div>
</template>

<script>
function pause(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

export default {

    data(){
        return {
            objects: [],
            net: null,
            running: false,
            legs: {
                left: null,
                right: null
            }
        }
    },

    mounted(){
        this.Start();
    },

    methods: {
        async Start(){
            

            await this.GetVideo();

            this.$refs.testvideo.play();

            this.net = await posenet.load();

            this.running = true;

            while(this.running == true){
                await this.Update();
                await pause(250);
            }
        },

        async GetVideo(){
            const stream = await navigator.mediaDevices.getUserMedia({
                audio: false,
                video: true
            });

            this.$refs.testvideo.srcObject = stream;

            return new Promise((resolve) => {
                this.$refs.testvideo.onloadedmetadata = () => {
                    console.log('bla');
                    resolve(this.$refs.testvideo);
                };
            });
        },

        async Update(){
            const scaleFactor = 0.50;
            const flipHorizontal = false;
            const outputStride = 32;
            // const imageElement = this.$refs.testimage;
            const videoElement = this.$refs.testvideo;
            
            try {
                const pose = await this.net.estimateSinglePose(document.getElementById('testvideo'), scaleFactor, flipHorizontal, outputStride);

                this.UpdatePose(pose);
            }catch(err){
                console.log(err);
            }
            
            
            //this.running = false;
        },

        UpdatePose(pose){
            console.log('update');
            let leftAnkle = pose.keypoints.find((point) => { return point.part == 'leftAnkle' })
            let rightAnkle = pose.keypoints.find((point) => { return point.part == 'rightAnkle' })

            this.legs = {
                left: leftAnkle.position,
                right: rightAnkle.position
            }
        },

        GetObjectStyle(position, image){
            return `
                top: ${position.y}px;
                left: ${position.x}px;
                background-image: url('/assets/images/${image}');
            `
        }

    }
}
</script>

<style lang="scss" scoped>

video, img {
    position: absolute;
    top: 0;
    left: 0;
    width: 800px;
    height: 600px;
    // width: auto;
    // min-width: 25vw;
    // height: 70vh;
    
}

.object {
    position: absolute;
    width: 80px;
    height: 300px;
    margin-left: -40px;
    margin-top: -25px;
    background-size: contain;
    background-repeat: no-repeat;
}

</style>


