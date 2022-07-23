<script>
import { computed, onMounted, ref } from "vue";
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import Vortex from './components/Vortex.vue';

export default {
    setup() {
        let apiKey = import.meta.env.VITE_SHEETS_KEY;
        let lines = ref([]);
        const getLines = async function () {
            await fetch("https://sheets.googleapis.com/v4/spreadsheets/1DucJ1Ul40FfM3xfU1NfTWwHXiVdqohqHrqBb21WAgRA/values/Sheet1?alt=json&key=" +
                apiKey, {
                method: "GET",
            })
                .then((res) => res.json())
                .then((data) => {
                console.log("data", data);
                lines.value = data.values;
                // remove column headers by removing first item in array
                lines.value.shift();
            });
        };
        onMounted(async () => {
            await getLines();
        });
        let seedFiveFirst = ref(0);
        let seedFiveSecond = ref(0);
        let seedSeven = ref(0);
        const getRandom = function (theLength) {
            return Math.floor(Math.random() * theLength);
        };
        let randomFiveFirst = computed(() => {
            if (lines.value) {
                let fives = lines.value.filter((line) => line[1] === "5");
                console.log("asdf", fives);
                let aThingToForceRecompute = seedFiveFirst.value;
                return fives[getRandom(fives.length)];
            }
            else {
                return [];
            }
        });
        let randomFiveSecond = computed(() => {
            if (lines.value) {
                let fives = lines.value.filter((line) => line[1] === "5");
                let aThingToForceRecompute = seedFiveSecond.value;
                return fives[getRandom(fives.length)];
            }
            else {
                return [];
            }
        });
        let randomSeven = computed(() => {
            if (lines.value) {
                let sevens = lines.value.filter((line) => line[1] === "7");
                let aThingToForceRecompute = seedSeven.value;
                return sevens[getRandom(sevens.length)];
            }
            else {
                return [];
            }
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
    components: { Vortex }
};
</script>

<template>
	<Vortex />
	<div v-if="lines" class="lines">
		<p>
			<button v-if="randomFiveFirst" @click="updateLine(5, 0)" type="done">
				{{ randomFiveFirst[0] }}
			</button>
		</p>
		<p>
			<button v-if="randomSeven" @click="updateLine(7, 0)" type="done">
				{{ randomSeven[0] }}
			</button>
		</p>
		<p>
			<button v-if="randomFiveSecond" @click="updateLine(5, 1)" type="done">
				{{ randomFiveSecond[0] }}
			</button>
		</p>
		<button @click="updateAll" class="button">Recompute</button>
	</div>
</template>

<style>
body {
	width: 100vw;
	height: 100vh;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	/* background-image: url('/vvvortex.svg');
	background-size: contain;
	background-repeat: no-repeat;
	background-position: center; */
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	background-color: #06083D;
	position: relative;
}
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	text-align: center;
	color: #2c3e50;
}
.lines {
		position: absolute;
	top: 0;
	left: 0;
	bottom: 0;
	right: 0;
	z-index: 2;
    height: 100%;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}
button {
	background-color: transparent;
	color: #fff;
	font-size: 2em;
	border: none;
	padding: 0.5em 1em;
	border-radius: none;
	cursor: pointer;
	box-shadow: none;
	font-family: inherit;
}
button:hover {
	text-decoration: underline;
	text-decoration-style: wavy;
	text-decoration-offset: 0.2em;
}
button.button {
	background-color: #2c3e50;
}
</style>
