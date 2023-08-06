<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import IssueSidebar from './components/IssueSidebar.vue';
import { onMounted, onUnmounted, ref } from 'vue';

let tamp = null
let currentUrl = ''
const showIssueTab = ref(false)
const reg = /\/issues/

onMounted(() => {
    // 定时检测URL是否发生变化
    tamp = setInterval(function() {
        if (window.location.href !== currentUrl) {
            console.log('URL发生变化');
            if(reg.test(window.location.href)) {
              showIssueTab.value = true
              document.documentElement.classList.add('issue-active')
              document.getElementById('app').classList.add('active')
            } else {
              showIssueTab.value = false
              document.documentElement.classList.remove('issue-active')
              document.getElementById('app').classList.remove('active')
            }
            currentUrl = window.location.href;
        }
    }, 500);
})

onUnmounted(() => {
    clearInterval(tamp)
})

</script>

<template>
  <IssueSidebar v-if="showIssueTab"/>
</template>

<style scoped>
</style>
