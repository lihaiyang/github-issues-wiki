<script setup>
 const props = defineProps({
  source: {
    type: Object,
    default: () => ({}),
  },
});

const handleClick = () => {
    const url = props.source.url
    const parser = new DOMParser();
    const oldTopo = document.getElementsByClassName('list-infinite')[0].scrollTop;

    fetch(url)
        .then(function(response) {
            return response.text();
        })
        .then(function(html) {
            const doc = parser.parseFromString(html, 'text/html');
            const app = document.getElementById('app')
            document.body = doc.body
            document.body.appendChild(app)
            document.getElementsByClassName('list-infinite')[0].scrollTop = oldTopo
        })
        .catch(function(error) {
            console.error('Failed to load issue:', error);
        });

        history.pushState({}, '', url);
}
</script>

<template>
  <div class="item-inner" @click="handleClick">
    <div class="head">
      <div class="index">{{ source.index }}</div>
      <div class="name" :title="source.text">{{ source.text }}</div>
    </div>
  </div>
</template>

<style lang="less" scoped>
.item-inner {
    text-align: left;
    border-top: 1px solid gray;
    padding: 10px 0;
  .head {
    font-weight: 500;
    display: flex;
    flex-direction: row;
  }
  .index {
    margin-right: 3px;
  }
  .name {
    margin-left: 5px;
    word-break: break-all;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2; /* 这里是超出几行省略 */
    overflow: hidden;
  }
  .desc {
    padding-top: 0.5em;
    text-align: justify;
  }
}

.item-inner:hover {
    background-color: #dcffff;
    cursor: default;
}
</style>