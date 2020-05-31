<template>
    <div>
        <!-- :value="now" @input="val=>value=val" -->
        <DatePicker v-model="now"></DatePicker>
    </div>
</template>
<script>
import DatePicker from './date-picker'
export default {
    components:{
        DatePicker
    },
    data(){
        return {
            now:new Date()
        }
    }
}
</script>
<style lang="stylus">
div {
}
</style>
