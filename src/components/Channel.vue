<template>
  <div class="channel">
    <div class="icon">
      <img 
        :src="channel.channelLogoPaths.SMALL" 
        alt="DSTV icon"
        width="50"
      >
    </div>
    <div class="name">
      {{ channel.channelName }}
    </div>
    <div class="number">
      {{ channel.channelNumber }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'Channel',
  props: [ 'channel', ],
}
</script>

<style scoped>
.channel {
  display: flex;
  padding: 1em;
  align-items: center;
  /* x, y, blur, spread */
  box-shadow: 1px 1px 10px 0px rgba(100, 100, 100, 0.5);
  border-radius: 5px;
  margin-bottom: 1em;
}

.channel > * {
  padding: 0 .5em;
}

.number {
  font-weight: 900;
  font-size: 2em;
}

.name {
  height: fit-content;
  flex-grow: 2;
}

</style>
