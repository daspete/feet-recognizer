<template>
    <div class="app">
        <recognizer/>
    </div>
</template>

<script>
import Recognizer from '~/components/Recognizer'

export default {
    components: {
        Recognizer
    },

    head: {
        script: [
            //{ src: '//cdnjs.cloudflare.com/ajax/libs/tracking.js/1.1.3/tracking-min.js' }
            { src: '//unpkg.com/@tensorflow/tfjs' },
            { src: '//unpkg.com/@tensorflow-models/posenet' }
        ]
    }
}
</script>
