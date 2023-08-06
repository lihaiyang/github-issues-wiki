<template>
    <VirtualList
        class="list-infinite"
        :data-key="'index'"
        :data-sources="listData"
        :data-component="Item"
        :estimate-size="70"
        :item-class="'list-item-infinite'"
        :footer-class="'loader-wrapper'"
        @totop="onScrollToTop"
        @tobottom="onScrollToBottom"
    >
        <template #footer v-if='hasFinished'>
            <div class="loader">已经到底了</div>
        </template>
    </VirtualList>
</template>

<script setup >
import { ref, onMounted } from 'vue';
import VirtualList from 'vue3-virtual-scroll-list';
import Item from './Item.vue';

let currentPage = 1
let hasFinished = ref(false)
const listData = ref([])
const onScrollToTop = () => {
  console.log('at top');
};

const onScrollToBottom = () => {
  console.log('at bottom');
  requestData()
};

onMounted(() => {
    requestData()
})

function requestData() {
    const url = `https://github.com${window.location.pathname.replace(/\/issues\/.*/, '\/issues')}?page=${currentPage}&q=is%3Aissue`;

    fetch(url)
        .then(function(response) {
            return response.text();
        })
        .then(function(html) {
            const parser = new DOMParser();
            const doc = parser.parseFromString(html, 'text/html');
            const issueLinks = doc.querySelectorAll('.js-navigation-open');
            const len = listData.value.length

            if(!issueLinks.length) {
                hasFinished.value = true
            } else {
                listData.value = listData.value.concat(Array.from(issueLinks).map((v,i) => {
                return {
                    index: len + i + 1,
                    text: v.innerText,
                    url: v.href
                }
            }))
            currentPage++;
            }
        })
        .catch(function(error) {
            console.error('Failed to load issues:', error);
        });
}
</script>

<style lang="less" scoped>
.list-infinite {
  width: 100%;
  height: 100%;
  border: 2px solid;
  border-radius: 3px;
  overflow-y: scroll;
  max-height: 100%;
  border-color: dimgray;
  position: relative;
  background-color: white;
  padding-left: 5px;
  padding-right: 5px;
  

  .list-item-infinite {
    display: flex;
    align-items: center;
    padding: 1em;
    border-bottom: 1px solid;
    border-color: lightgray;
  }

  .loader {
    margin-top: 5px;
    letter-spacing: 3px;
  }
}
</style>
