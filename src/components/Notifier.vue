<template>
  <div id="notify" :class="classes">
    <div>{{current.text || 'Vuoto'}}</div>
    <button @click="goNext">
      <i class="fa fa-close" v-if="isLast">&nbsp;</i>
      <div v-else-if="count">Next ({{count - 1}})</div>
    </button>
		<div class="progress" :style="{ width: (timePerc * 100) + '%'}"></div>
  </div>
</template>

<script>
import EventBus from '@/components/eventBus.js'

export default {
  name: 'Notifier',
  data () {
			return {
				queue: [],
				changing: false,
				timeout: null,
				interval: null,
				currentTimestamp: null
			}
		},
		computed: {
			current () {
				return this.queue.length ? this.queue[0] : {}
			},
			timePerc () {
				if (this.current && this.current.showed) {
					return (this.currentTimestamp - this.current.showed) / this.current.timeout
				}
				return 0.5
			},
			count () {
				return this.queue.length
			},
			isLast () {
				return this.queue.length === 1
			},
			open () {
				return !!this.queue.length && !this.changing
			},
			classes () {
				var classes = ''
				if (!this.open) {
					classes += 'hide '
				}
				if (this.current && this.current.type) {
					classes += this.current.type
				}
				return classes
			}
		},
		methods: {
			goNext () {
				this.changing = true
				setTimeout(() => {
					this.changing = false
					this.queue.shift()
				}, 300)
			},
			showError (payload) {
				this.show('error', payload)
			},
			showSuccess (payload) {
				this.show('success', payload)
			},
			showInfo (payload) {
				this.show('info', payload)
			},
			showWarning (payload) {
				this.show('warning', payload)
			},
			show (type, payload) {
				if (typeof payload === 'string') {
					payload = { text: payload }
				}
				payload.type = type
				if (!payload.timeout) {
					if (type === 'warning') {
						payload.timeout = 5000
					} else if (type !== 'error') {
						payload.timeout = 3000
					}
				}
				var replaced = false
				if (payload.key) {
					var index = this.queue.findIndex(q => q.key === payload.key)
					if (index > -1) {
						this.queue.splice(index, 1, payload)
						replaced = true
					}
				}
				if (!replaced) {
					this.queue.push(payload)
				}
				/* if (!this.timeout) {
					this.change()
				} */
			},
			/* change () {
				this.queue.shift()
				if (this.queue.length) {
					this.timeout = setTimeout(this.change, 3000)
				} else {
					this.timeout = null
				}
			} */
			updateCurrent (tm) {
				this.currentTimestamp = (new Date()).getTime()
			}
		},
		mounted () {
			EventBus.$on('notify:error', this.showError)
			EventBus.$on('notify:success', this.showSuccess)
			EventBus.$on('notify:info', this.showInfo)
			EventBus.$on('notify:warning', this.showWarning)

			this.interval = setInterval(this.updateCurrent, 50)
		},
		beforeDestroy () {
			EventBus.$off('notify:error', this.showError)
			EventBus.$off('notify:success', this.showSuccess)
			EventBus.$off('notify:info', this.showInfo)
			EventBus.$off('notify:warning', this.showWarning)

			clearInterval(this.interval)
		},
		watch: {
			current (newV) {
				console.log('current msg changed')
				if (this.timeout) {
					clearTimeout(this.timeout)
					this.timeout = null
				}
				if (newV && newV.timeout) {
					console.log('reset timeout')
					this.timeout = setTimeout(this.goNext, newV.timeout)
					this.current.showed = (new Date()).getTime()
				}		
			}
		}
}
</script>

<style scoped lang="scss">
$black: #50514F;
$white: #fbfbfb;
$red: lighten(#FF3C38, 5%);
$orange: #FF8C42;
$yellow: #FFF275;
$green: #8BB86D;
$primary: #66C3FF;


#notify, .notifier * {
	padding: 0;
	margin: 0;
	box-sizing: border-box;
}
#notify {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 9000;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  padding: .5rem;
  background-color: $black;
	color: $white;
  font-size: 1rem;
  box-shadow: 0 0 5px $black, 0 0 10px $black, 0 0 15px $black;
  transition: all .3s ease;
  will-change: transform, background-color;

  &.hide {
    transform: translateY(-100%);
    box-shadow: 0 0 0 $black;
  }

  button {
    background-color: transparent;
    border-color: transparent;
    color: inherit;
		cursor: pointer;
		outline: none;
		font-weight: 500;
    font-size: 1rem;
  }
	
	&.error {
		background: $red;
		color: $white;
	}
	
	&.warning {
		background: mix($yellow, $orange, 60%);
		color: $black;
	}
	
	&.info {
		background: $primary;
		color: $white;
	}
	
	&.success {
		background: $green;
		color: $white;
	}

	.progress {
		position: absolute;
		bottom: 0;
		height: 2px;
		left: 0;
		width: 0;
		background: $black;
		transition: width 45ms linear;
	}
}
</style>
