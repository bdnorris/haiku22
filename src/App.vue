<script>
import { computed, onMounted, ref } from "vue";
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup

export default {
	setup() {
		let apiKey = import.meta.env.VITE_SHEETS_KEY;
		let lines = ref([]);
		const getLines = function () {
			fetch(
				"https://sheets.googleapis.com/v4/spreadsheets/1DucJ1Ul40FfM3xfU1NfTWwHXiVdqohqHrqBb21WAgRA/values/Sheet1?alt=json&key=" +
					apiKey,
				{
					method: "GET",
				}
			)
				.then((res) => res.json())
				.then((data) => {
					console.log(data);
					lines.value = data.values;
					// remove column headers by removing first item in array
					lines.value.shift();
				});
		};
		onMounted(() => {
			getLines();
		});

    let seed = ref(0);
    
    const getRandom = function (theLength) {
      return Math.floor(Math.random() * theLength);
    };

		let randomFive = computed(() => {
			let fives = lines.value.filter((line) => {
        return line[1] == "5";
			});
      // console.log('fives', fives);
			let randomOne = fives[getRandom(fives.length)];
			let randomTwo = fives[getRandom(fives.length)];
      let aThingToForceRecompute = seed.value
			if (randomOne === randomTwo) {
				randomTwo = fives[getRandom(fives.length)];
				return [randomOne, randomTwo];
			} else {
				return [randomOne, randomTwo];
			}
		});
		let randomSeven = computed(() => {
			let sevens = lines.value.filter((line) => {
				return line[1] == "7";
			});
      let aThingToForceRecompute = seed.value
			return sevens[getRandom(sevens.length)];
		});


		return {
			lines,
			randomFive,
			randomSeven,
      seed
      // getRandom
		};
	},
};
</script>

<template>
	<div v-if="lines">
		<p v-if="randomFive[0]">
			{{ randomFive[0][0] }}
		</p>
		<p v-if="randomSeven">
			{{ randomSeven[0] }}
		</p>
		<p v-if="randomFive[1]">
			{{ randomFive[1][0] }}
		</p>
		<!-- <ul v-for="(line, index) in lines" :key="index">
			<li>{{ line[0] }}</li>
		</ul> -->
    <button @click="seed++">Recompute</button>
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
