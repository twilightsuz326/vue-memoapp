<template>
    <div class="event-page">
        <h2>Event：イベント</h2>

        <!-- 従来のJavaScript -->
        <h3><span class="badge badge-secondary">JavaScript</span></h3>
        <span id="countText">クリックされた回数は0回です </span>
        <button id="button" class="btn btn-sm btn-info">Click Me</button>
        <br />
        <br />

        <!-- Vue.js -->
        <h3><span class="badge badge-success">Vue.js</span></h3>
        <span>クリックされた回数は{{ count }}回です</span>
        <button class="btn btn-sm btn-info" @click="countUp">Click Me</button>
    </div>
</template>

<script>
// 従来のJavaScript
window.onload = function () {
    let count = 0;
    const button = document.getElementById('button');
    button.onclick = function () {
        count++;
        const countText = document.getElementById('countText');
        countText.innerHTML = 'クリックされた回数は' + count + '回です';
    };
}
export default {
    data() {
        return {
            count: 0,
        }
    },
    methods: {
        countUp() {
            this.count++;
        }
    }
}
</script>

<style scoped>
.event-page {
    padding: 20px;
}
</style>