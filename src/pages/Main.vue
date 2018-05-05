<template>
<div class="wrapp">
	<transition-group :name="directionAnimation"  >
		<slide0 v-show="isSlide(0)" class="pages" :key="0"/>
		<!-- Carousel-->
		<slide1 v-show="isSlide(1)" class="pages" :key="1"/>
		<slide2 v-show="isSlide(2)" class="pages" :key="2"/>
		<slide3 v-show="isSlide(3)" class="pages" :key="3"/>
		<slide4 v-show="isSlide(4)" class="pages" :key="4"/>
		<slide5 v-show="isSlide(5)" class="pages" :key="5"/>
	</transition-group>
	<ul class="slide-control">
		<li
			v-for="(val ,index) in slidesEL"
			:key="index"
			class="slide-markers"
			:class="{'slide-markers--active': isMarkerActive(index)}"
			@click="toogleSlide(index)"
		>
			<span class="slide-marker__num">{{setMarkerNum(index)}}</span> <span class="slide-marker__line"></span>
		</li>
	</ul>
</div>
</template>

<script>
import {eventBus} from '../eventBus.js'
import Slide0 from '@/components/slides/Slide_1'
import Slide1 from '@/components/slides/Carousel'
import Slide2 from '@/components/slides/Slide_3'
import Slide3 from '@/components/slides/Slide_4'
import Slide4 from '@/components/slides/Slide_5'
import Slide5 from '@/components/slides/Slide_6'

const settingsScroll = {
	direction: {
		horizontalLf: 'horizontal-left',
		horizontalRt: 'horizontal-right',
		vertical: 'vertical',
		mix: 'mix'
	}
}
export default {
	name: 'Main',
	components: {Slide0, Slide1, Slide2, Slide3, Slide4, Slide5},
	data () {
		return {
			scroll: true,
			scrollVAlue: -1,
			slidesEL: ['Slide0', 'Slide1', 'Slide2', 'Slide3', 'Slide4', 'Slide5'],
			firstSlide: 0,
			showSlide: 0,
			nextPrewSlide: 'next',
			scrollDirection: settingsScroll.direction.vertical,
			scrollUpAnimations: ['scroll-up', 'scroll-lf', 'scroll-rt'],
			scrollDnAnimations: ['scroll-dn', 'scroll-lf', 'scroll-rt'],
			touchStart: {
				x: '',
				y: ''
			},
			touchEnd: {
				x: '',
				y: ''
			},
			touchDirection: ''
		}
	},
	created () {
		document.addEventListener('wheel', (event) => {
			this.scrollVAlue = event.deltaY
			this.trackWheel()
		})
		document.addEventListener('touchstart', (event) => {
			this.touchStart.x = event.changedTouches[0].pageX
			this.touchStart.y = event.changedTouches[0].pageY
		})

		document.addEventListener('touchend', (event) => {
			this.touchEnd.x = event.changedTouches[0].pageX
			this.touchEnd.y = event.changedTouches[0].pageY
			this.movementTouch()
			this.flippingPage()
		})
	},
	methods: {
		trackWheel () {
			if (this.scroll === true) {
				setTimeout(() => {
					this.scrollPage()
					this.scroll = true
				}, 400)
				this.scroll = false
			}
		},
		flippingPage () {
			if (this.touchDirection === 'bottom') {
				this.nextSlide()
			} else if (this.touchDirection === 'top') {
				this.prewSlide()
			}
		},
		scrollPage () {
			if (this.scrollVAlue > 0) {
				this.nextSlide()
			} else if (this.scrollVAlue < 0) {
				this.prewSlide()
			}
		},
		prewSlide () {
			this.nextPrewSlide = 'prew'
			if (this.showSlide > this.firstSlide) {
				this.showSlide--
			} else {
				this.showSlide = this.maxSlide
			}
		},
		nextSlide () {
			this.nextPrewSlide = 'next'
			if (this.showSlide < this.maxSlide) {
				this.showSlide++
			} else {
				this.showSlide = this.firstSlide
			}
		},
		isSlide (index) {
			return this.slidesEL[this.showSlide] === this.slidesEL[index]
		},
		randomDirect (arr) {
			let randomEl = Math.floor(Math.random() * (3 - 0) + 0)
			return arr[randomEl]
		},
		isMarkerActive (id) {
			return id === this.showSlide
		},
		toogleSlide (id) {
			if (id > this.showSlide) {
				this.nextPrewSlide = 'next'
			} else if (id < this.showSlide) {
				this.nextPrewSlide = 'prew'
			}
			this.showSlide = id
		},
		setMarkerNum (i) {
			return ++i
		},
		movementTouch () {
			let stepX = Math.abs(this.touchEnd.x - this.touchStart.x)
			let stepY = Math.abs(this.touchEnd.y - this.touchStart.y)

			if (stepX > stepY) {
				if (this.touchEnd.x > this.touchStart.x) {
					this.touchDirection = 'right'
				} else {
					this.touchDirection = 'left'
				}
				eventBus.$emit('move', this.touchDirection)
			} else if (stepX < stepY) {
				if (this.touchEnd.y > this.touchStart.y) {
					this.touchDirection = 'bottom'
				} else {
					this.touchDirection = 'top'
				}
			}
		}
	},
	computed: {
		maxSlide () {
			return	this.slidesEL.length - 1
		},
		directionAnimation () {
			if (this.scrollDirection === settingsScroll.direction.vertical) {
				if (this.nextPrewSlide === 'next') {
					return 'scroll-dn'
				} else if (this.nextPrewSlide === 'prew') {
					return 'scroll-up'
				}
			} else if (this.scrollDirection === settingsScroll.direction.horizontalLf) {
				if (this.nextPrewSlide === 'next') {
					return 'scroll-lf'
				} else if (this.nextPrewSlide === 'prew') {
					return 'scroll-rt'
				}
			} else if (this.scrollDirection === settingsScroll.direction.horizontalRt) {
				if (this.nextPrewSlide === 'next') {
					return 'scroll-rt'
				} else if (this.nextPrewSlide === 'prew') {
					return 'scroll-lf'
				}
			} else if (this.scrollDirection === settingsScroll.direction.mix) {
				if (this.nextPrewSlide === 'next') {
					let direction = this.randomDirect(this.scrollUpAnimations)
					return direction
				} else if (this.nextPrewSlide === 'prew') {
					let direction = this.randomDirect(this.scrollDnAnimations)
					return direction
				}
			}
		}
	}
}
</script>

<style lang="scss" scoped>
.wrapp {
	position: relative;
	width: 100%;
	height: 100vh;
	overflow: hidden;
}
.pages {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100vh;
}
// animation scroll
$animationSpeed: .5s;
$animTimFun: ease-out;
// scroll UP
.scroll-up-enter-active {
		animation: slideDown $animationSpeed $animTimFun;
}
.scroll-up-leave-active {
		animation: slideUp $animationSpeed $animTimFun;
}

// scroll Down
.scroll-dn-enter-active {
		animation:  slideUp $animationSpeed $animTimFun reverse;
}
.scroll-dn-leave-active {
		animation: slideDown $animationSpeed $animTimFun reverse;
}

// scroll Left
.scroll-lf-enter-active {
		animation:  slideRight  $animationSpeed $animTimFun ;
}
.scroll-lf-leave-active {
		animation:  slideLeft $animationSpeed $animTimFun ;
}

// scroll Right
.scroll-rt-enter-active {
		animation:   slideLeft  $animationSpeed $animTimFun reverse;
}
.scroll-rt-leave-active {
		animation: slideRight $animationSpeed $animTimFun reverse;
}

@keyframes slideUp {
	from {
		transform: translateY(0);
	}
	to {
		transform: translateY(100vh);
	}
}
@keyframes slideDown {
	from {
		transform: translateY(-100vh);
	}
	to {
		transform: translateY(0);
	}
}
@keyframes slideLeft {
	from {
		transform: translateX(0);
	}
	to {
		transform: translateX(-100%);
	}
}
@keyframes slideRight {
	from {
		transform: translateX(100%);
	}
	to {
		transform: translateX(0);
	}
}

// markers
.slide-control {
	position: fixed;
	right: 20px;
	top: 45%;
	list-style: none;
	padding: 0;
	margin: 0;
}
.slide-markers {
	margin: 12px 0;
	display: flex;
	align-items: center;
	cursor: pointer;
}
.slide-marker__num {
	font-size: 16px;
	width: 20px;
	height: 20px;
	line-height: 18px;
	background-color: rgba(0,0,0, .7);
	color: #fff;
	border-radius: 50%;
	text-align: center;
	opacity: 0;
	transition: opacity .3s;
}
.slide-marker__line {
	width: 40px;
	height: 3px;
	margin-left: 10px;
	background-color: rgba(0,0,0, .4);
	transition: all .3s;
}

.slide-markers--active {
	.slide-marker__num {opacity: 1;}

	.slide-marker__line {
		transform: scale(1.2);
		background-color: rgba(0,0,0, .9);
	}
}
</style>
