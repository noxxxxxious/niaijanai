<template>
	<section>
		<div class="settings-panel-container">
			<div class="settings-panel">
				<div class="panel-title">
					設定
				</div>
				<div class="option-container">
					<div class="option-title">
						Batch Size
					</div>
					<div class="option-content">
						<div class="batch-size">
							<div>Size</div>
							<input type="number" @blur="checkLimits('batch')" v-model="batchSize"/>
						</div>
					</div>
				</div>
				<div class="option-container">
					<div class="option-title">
						Grouping
					</div>
					<div class="option-content">
						<div class="group-kanji">
							<div class="level-limit-option-title">Group Similar Kanji</div>
							<input type="checkbox" v-model="groupKanji" />
						</div>
					</div>
				</div>
				<div class="option-container">
					<div class="option-title">Level Limits</div>
					<div class="option-content">
						<div class="level-limit">
							<div class="level-limit-option-title" :class="upperLimitDisabled">Upper Limit</div>
							<input type="number" @blur="checkLimits('upper')" v-model="levelLimitUpper" :disabled="useWaniKaniLevel" />
						</div>
						<div class="level-limit">
							<div class="level-limit-option-title">Lower Limit</div>
							<input type="number" @blur="checkLimits('lower')" v-model="levelLimitLower" />
						</div>
						<div class="level-limit">
							<div class="level-limit-option-title">Use WaniKani Level</div>
							<input type="checkbox" v-model="useWaniKaniLevel" />
						</div>
					</div>
				</div>
				<div class="option-container">
					<div class="option-title">
						WaniKani
					</div>
					<div class="option-content">
						<div class="wanikani">
							<div>API Key</div>
							<input type="password" placeholder="API Key Goes Here" />
						</div>
					</div>
				</div>
				<div class="save-button-container">
					<button class="save-button" @click="updateValues">
						Save
					</button>
				</div>
			</div>
		</div>
	</section>
</template>

<script setup lang='ts'>
	import { ref, computed, defineEmits } from 'vue'
	const batchSize = ref(5)
	const groupKanji = ref(true)
	const useWaniKaniLevel = ref(false)
	const upperLimitDisabled = computed(() => useWaniKaniLevel.value ? "disabled" : "")
	const levelLimitUpper = ref(60)
	const levelLimitLower = ref(1)

	const emit = defineEmits(['settingsUpdated']);

	function checkLimits(inLimit) {
		if(inLimit == "upper") {
			if(levelLimitUpper.value > 60) levelLimitUpper.value = 60
			else if(levelLimitUpper.value < 1) levelLimitUpper.value = 1
		} else if(inLimit == "lower") {
			if(levelLimitLower.value  > 60) levelLimitLower.value = 60 
			else if(levelLimitLower.value  < 1) levelLimitLower.value = 1
		} else if(inLimit == "batch") {
			if(batchSize.value > 20) batchSize.value = 20
			else if(batchSize.value < 1) batchSize.value = 1
		} else {
			throw new Error(`Running checkLimits('${inLimit}'), but no case for ${inLimit}`)
		}
	}

	function updateValues() {
		emit('settingsUpdated', {
			batchSize: batchSize.value,
			groupKanji: groupKanji.value,
			levelLimit: {
				upper: levelLimitUpper.value, //TODO: Change this to check for WaniKani usage
				lower: levelLimitLower.value
			}
		})
	}
</script>

<style scoped>
	section {
		height: calc(100dvh - 64px);
		width: 300px;
		padding: 0px 10px;
	}
	.settings-panel-container {
		height: 100%;
		padding: 10px;
		background-color: #414559;
		border-radius: 20px;
		box-shadow: 2px 2px 2px #0004;
		transition: translate 0.3s, box-shadow 0.3s;
	}
	.settings-panel-container:hover {
		translate: -1px -1px;
		box-shadow: 4px 4px 8px #0007;
	}
	.settings-panel {
		display: flex;
		flex-direction: column;
		gap: 10px;
		height: 100%;
		background-color: #737994;
		border-radius: 10px;
		box-shadow: 2px 2px 2px #0004;
		padding: 10px;
	}
	.panel-title {
		display: flex;
		align-items: center;
		justify-content: center;
		user-select: none;
		font-family: 'MikanNoki';
		font-size: 30px;
		font-weight: bold;
		color: #c6d0f5;
		-webkit-text-stroke: 1pt #303446;
		text-shadow: 2px 2px 2px #0007;
		letter-spacing: -3px;
		transition: translate 0.3s, text-shadow 0.3s;
	}
	.panel-title:hover {
		translate: -1px -1px;
		text-shadow: 4px 4px 8px #0007;
	}
	.option-container {
		display: flex;
		flex-direction: column;
		color: #c6d0f5; 
		border-radius: 5px;
		box-shadow: 2px 2px 2px #0007;
		transition: translate 0.3s, box-shadow 0.3s;
	}
	.option-container:hover {
		translate: -1px -1px;
		box-shadow: 4px 4px 8px #0007;
	}
	.option-title {
		padding: 5px 10px;
		border-radius: 5px 5px 0px 0px;
		background-color: #303446;
	}
	.option-content {
		display: flex;
		flex-direction: column;
		gap: 5px;
		padding: 10px;
		border-radius: 0px 0px 5px 5px;
		background-color: #51576d;
	}
	input {
		all: unset;
		text-align: center;
		width: 30px;
		background-color: #414559;
		border-radius: 5px;
		box-shadow: inset 0px 0px 2px #0004;
		transition: color 0.3s;
	}
	input::-webkit-outer-spin-button,
	input::-webkit-inner-spin-button {
		-webkit-appearance: none;
		margin: 0; /* <-- Apparently some margin are still there even though it's hidden */
	}
	input[type="checkbox"] {
		background-color: #ea999c;
		transition: background-color 0.3s;
	}
	input[disabled] {
		color: #51576d;
	}
	input[type="checkbox"]:checked {
		background-color: #a6d189;
	}
	input[type="checkbox"]:hover {
		cursor: pointer;
	}
	.batch-size,
	.group-kanji,
	.level-limit {
		display: grid;
		grid-template-columns: 1fr 50px;
		justify-items: center;
	}
	.wanikani {
		text-align: center;
	}
	.wanikani input {
		width: 100%;
	}
	.wanikani input::placeholder {
		color: #626880;
	}
	.save-button-container {
		align-self: flex-end;
		display: flex;
		align-items: center;
		padding: 10px 20px;
		background-color: #414559;
		border-radius: 10px;
		box-shadow: 2px 2px 2px #0004;
		transition: translate 0.3s, box-shadow 0.3s;
	}
	.save-button-container:has(> .save-button:hover) {
		translate: 1px 1px;
		box-shadow: 1px 1px 1px #0007;
	}
	.save-button {
		all: unset;
		display: block;
		margin: auto;
		width: 100px;
		height: 40px;
		text-align: center;
		border-radius: 5px;
		background-color: #babbf1;
		box-shadow: 2px 2px 2px #0004;
		transition: translate 0.3s;
	}
	.save-button:hover {
		cursor: pointer;
		background-color: #cacbff;
		translate: -2px -2px;
	}
</style>
