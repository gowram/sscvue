<script setup>
import { Peer } from 'socket:peer'
import { ref, onMounted } from 'vue'



onMounted(async () => {
  const pair = await Peer.createKeys()
  const clusterId = await Peer.createClusterId()
  peer = new Peer({ ...pair, clusterId })
  peer.join()
  peer.on('greeting', (data, ppr, address, port) => {
    clr.value = "color = " + data.color;
    if (peer.peer.address !== ppr) {
      console.log('remote on...')     
    }
    setColor(data.color)
  })
})

defineProps({
  msg: String,
})

const count = ref(0)

let peer = ref("")
let ip = ref("loading....")
let clr = ref("black")

const setColor = c => document.body.style.backgroundColor = `#${c}`

function setBgColor() {
  const color = Math.floor(Math.random() * 16777215).toString(16)
 
  peer.emit('greeting', {
    'color': color,
    'timestamp': Date.now()
  })
  setColor(color);
  ip.value = peer?.peer?.address
} 
</script>

<template>
  <h1>{{ msg }}</h1>
  <h2>{{ ip }}</h2>
  <h3>{{ clr }}</h3>
  <div class="card">
    <button type="button" @click="setBgColor">Change Color</button>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
