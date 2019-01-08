<template>
  <div id="app">
    <notifier></notifier>

    <div>
      <button @click="testError">Test error</button>
      <button @click="testWarning">Test warning</button>
      <button @click="testInfo">Test info</button>
      <button @click="testSuccess">Test success</button>
    </div>
		<br />
		<div>
			<label>
				<input type="radio" v-model="editPrevious" :value="false" />
				Create new notify
			</label>
			
			<label>
				<input type="radio" v-model="editPrevious" :value="true" />
				Edit last notify
			</label>
		</div>
  </div>
</template>

<script>
import Notifier from './Notifier.vue'
import EventBus from './eventBus'

export default {
  name: 'app',
  components: {
    Notifier
  },
  data () {
    return {
      editPrevious: false,
			lastKey: '4ssdf'
    }
  },
  methods: {
		testError () {
			if (!this.editPrevious) {
				this.lastKey = '' + Math.random()
			}
			EventBus.$emit('notify:error', {
				text: 'Test error',
				key: this.lastKey
			})
		},
		testWarning () {
			if (!this.editPrevious) {
				this.lastKey = '' + Math.random()
			}
			EventBus.$emit('notify:warning', {
				text: 'Test warning',
				key: this.lastKey
			})
		},
		testInfo () {
			if (!this.editPrevious) {
				this.lastKey = '' + Math.random()
			}
			EventBus.$emit('notify:info', {
				text: 'Test info',
				key: this.lastKey
			})
		},
		testSuccess () {
			if (!this.editPrevious) {
				this.lastKey = '' + Math.random()
			}
			EventBus.$emit('notify:success', {
				text: 'Test success',
				key: this.lastKey
			})
		}
	}
}
</script>

<style lang="scss">
$black: #50514F;
$white: #fbfbfb;

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
	position: fixed;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	background: $black;
	color: $white;
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	font-family: 'Roboto', sans-serif;
}

</style>
