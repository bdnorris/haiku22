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
					console.log('data', data);
					lines.value = data.values;
					// remove column headers by removing first item in array
					lines.value.shift();
				});
		};
		onMounted(() => {
			getLines();
		});

		let seedFiveFirst = ref(0);
		let seedFiveSecond = ref(0);
		let seedSeven = ref(0);

		const getRandom = function (theLength) {
			return Math.floor(Math.random() * theLength);
		};

		let randomFiveFirst = computed(() => {
      let fives = lines.value.filter(
        (line) => line[1] === "5"
      )
      console.log('asdf', fives)
			let aThingToForceRecompute = seedFiveFirst.value;
			return fives[getRandom(fives.length)];
		});
		let randomFiveSecond = computed(() => {
      let fives = lines.value.filter(
        (line) => line[1] === "5"
      )
			let aThingToForceRecompute = seedFiveSecond.value;
			return fives[getRandom(fives.length)];
		});
		let randomSeven = computed(() => {
      let sevens = lines.value.filter(
        (line) => line[1] === "7"
      )
			let aThingToForceRecompute = seedSeven.value;
			return sevens[getRandom(sevens.length)];
		});
		const updateLine = function (syls, index) {
			if (syls === 5 && index === 0) {
				seedFiveFirst.value++;
			}
      if (syls === 5 && index === 1) {
        seedFiveSecond.value++;
      }
			if (syls === 7) {
				seedSeven.value++;
			}
		};
		const updateAll = function () {
			seedFiveFirst.value++;
			seedFiveSecond.value++;
			seedSeven.value++;
		};

		return {
			lines,
			randomFiveFirst,
			randomFiveSecond,
			randomSeven,
			updateLine,
			updateAll,
		};
	},
};
</script>

<template>
	<div v-if="lines">
		<p>
			<button v-if="randomFiveFirst[0]" @click="updateLine(5, 0)" type="done">
				{{ randomFiveFirst[0] }}
			</button>
		</p>
		<p>
			<button v-if="randomSeven" @click="updateLine(7, 0)" type="done">
				{{ randomSeven[0] }}
			</button>
		</p>
		<p>
			<button v-if="randomFiveSecond[1]" @click="updateLine(5, 1)" type="done">
				{{ randomFiveSecond[0] }}
			</button>
		</p>
		<button @click="updateAll">Recompute</button>
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
