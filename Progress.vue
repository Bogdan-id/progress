<template>
<!-- 
	In case if some question arise, you can see full code in my test app at the link below 
	[LINK]
-->
<div>
	<v-snackbar
		absolute
		v-model="state"
		top
		class="mt-12 pt-2"
		color="grey lighten-5 black--text"
		:timeout="0"
		>
		<v-progress-linear
			rounded
			v-model="currentProgress"
			class="white--text"
			height="5"
			reactive
			color="green darken-3"
			>
		</v-progress-linear>
	</v-snackbar>
	<v-overlay>
	</v-overlay>
</div>
</template>

<script>
import { progressUtils } from '../utils.js'

export default {
	data: () => ({
		progress: progressUtils,
		intervalInst: null,
		interval: 140,
		progressMax: 100,
		emulatePreMax: 85,
		spinnerProp: {
			duration: 1000,
			size: 70,
			color: 'rgb(236, 232, 229)'
		},
	}),
	computed: {
		currentProgress: {
			get() {
				return this.progress.getProgress().currentProgress
			},
			set(val) {
				this.progress.setProgress(val)
			}
		},
		emulate() {
			return this.progress.getProgress().emulate
		},
		spinner() {
			return this.progress.getProgress().spinner
		},
		state() {
			return this.progress.getProgress().state
		},
	},
	methods: {
		progressInterval () {
			this.intervalInst = setInterval(() => {
				this.currentProgress += Math.random() * (14 - 3) + 1
			}, this.interval)
		},
		finishLoad(val) {
			if(val >= this.emulatePreMax && this.emulate && val !== this.progressMax) {
				this.currentProgress = val
				this.emulateHandler(clearInterval, this.intervalInst)
			} else if(val >= this.progressMax) {
				setTimeout(() => {
					this.progress.finishProgress()
				}, 70)
				this.emulateHandler(clearInterval, this.intervalInst)
			}
		},
		emulateHandler(callback, params) {
			if(this.emulate && typeof callback === 'function'){
				callback(params)
			} else return
		}
	},
	watch: {
		currentProgress(val) {
			this.finishLoad(val)
		}
	},
	mounted() {
		this.emulateHandler(this.progressInterval)
	},
}
</script>