<template>
  <div class="pod">
    <h3>{{ title }}</h3>

    <div class="pod-details">
      <span>By {{ name }}</span> /
      <span>{{ date }}</span>

      <audio controls>
        <source :src="source" type="audio/mpeg" />
        Your browser does not support the audio element.
      </audio>
    </div>
  </div>
</template>

<script>
export default {
  name: "Podcast",
  props: {
    title: String,
    name: String,
    date: String,
    source: URL,
  },
};
</script>

<style>
h3 {
  font-weight: bolder;
}

.pod {
  background-color: #45a049;
  border-radius: 5px;
  padding: 10px;
  margin: 5px;
}

.pod-details {
  background-color: rgb(255, 255, 255);
  display: block;
  padding: 5px;
  margin-bottom: 15px;
  border-radius: 10px;
}

span {
  margin-left: 5px;
  margin-right: 5px;
  color: rgb(0, 0, 0);
}

audio {
  margin-top: 15px;
  display: block;
}
</style>
