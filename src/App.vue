<script>
import { computed, onMounted, ref } from 'vue';
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup

export default {
	setup() {
    let apiKey = import.meta.env.VITE_SHEETS_KEY;
    let lines = ref([]);
		const getLines = function () {
      fetch(
				"https://sheets.googleapis.com/v4/spreadsheets/1DucJ1Ul40FfM3xfU1NfTWwHXiVdqohqHrqBb21WAgRA/values/Sheet1?alt=json&key=" + apiKey,
				{
					method: "GET",
				}
			)
      .then((res) => res.json())
      .then((data) => {
        console.log(data)
        lines.value = data.values;
        // remove column headers by removing first item in array
        lines.value.shift();
      })
    }
    onMounted(() => {
      getLines()
    })

		return {
			lines
		};
	},
};
</script>

<template>
	<div v-if="lines">
    <h2>lines</h2>
		<ul v-for="(line, index) in lines" :key="index">
      <li>{{ line[0] }}</li>
    </ul>
	</div>
</template>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>
