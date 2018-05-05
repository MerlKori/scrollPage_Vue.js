<template>
<section class="carousel">
	<ul class="carousel__slides-wrap">
		<transition-group :name="slideTo">
		<li
			v-for="(val , key, index) in imgSrc"
			:key="index"
			v-show="isActiveSlide (index)"
			class="carousel__slide"
			:style="{backgroundImage: 'url(' + val + ')' }"
		>
		</li>
		</transition-group>
	</ul>
	<button
			@click="prewCarouselSlide()"
			class="carousel__control-btn prew"></button>
	<button
			@click="nextCarouselSlide()"
			class="carousel__control-btn next"></button>
</section>
</template>

<script>
import {eventBus} from '../../eventBus.js'
export default {
	name: 'Carousel',
	props: {
		touchDirection: {
			type: String
		}
	},
	data () {
		return {
			imgSrc: {
				src1: '../../../static/images/carousel/1.jpg',
				src2: '../../../static/images/carousel/2.jpg',
				src3: '../../../static/images/carousel/3.jpg'
			},
			activeSlide: 0,
			startSlide: 0,
			slideTo: 'carousel-flipp-lf'
		}
	},
	created () {
		eventBus.$on('move', (data) => {
			this.flippingCarousel(data)
		})
	},
	methods: {
		isActiveSlide (i) {
			return i === this.activeSlide
		},
		prewCarouselSlide () {
			this.slideTo = 'carousel-flipp-lf'
			if (this.activeSlide > this.startSlide) {
				this.activeSlide--
			} else {
				this.activeSlide = this.maxCaruselSlide
			}
		},
		nextCarouselSlide () {
			this.slideTo = 'carousel-flipp-rt'
			if (this.activeSlide < this.maxCaruselSlide) {
				this.activeSlide++
			} else {
				this.activeSlide = this.startSlide
			}
		},
		flippingCarousel (direction) {
			if (direction === 'left') {
				this.nextCarouselSlide()
			} else if (direction === 'right') {
				this.prewCarouselSlide()
			}
		}
	},
	computed: {
		maxCaruselSlide () {
			return	 Object.keys(this.imgSrc).length - 1
		}
	}
}
</script>

<style lang="scss" scoped>
.carousel__slides-wrap {
	display: block;
	position: relative;
}
.carousel__slide {
	position: absolute;
	top: 0;
	left: 0;
	display: block;
	width: 100%;
	height: 100vh;
	background-repeat: no-repeat;
	background-position: 0 0;
	background-size: cover;
}
// controll
$bd-color: rgba(0,0,0, .4);
$bd-width: 7px;
$btn-size: 2.5rem;
$px-btn: 6.25rem;
$active-scale: .85;

.carousel__control-btn {
	position: absolute;
	top: 40%;
	background-color: transparent;
	border-radius: 5px;
	width: $btn-size;
	height: $btn-size;
	border-top: $bd-width solid $bd-color;
	border-left: $bd-width solid $bd-color;
	cursor: pointer;
	transition: border-color.3s, transform .25s;

	&.prew {
		left: $px-btn;
		transform: rotate(- 45deg);
	}
	&.next {
		right: $px-btn;
		transform: rotate(135deg);
	}

	&:hover {
		border-color: rgba(0,0,0, .8);
	}
	&:active {
		&.prew {
		transform: rotate(- 45deg) scale($active-scale);
		}
		&.next {
			transform: rotate(135deg) scale($active-scale);
		}
	}
}
// toggle animation
$animationSpeed: .5s;
$animTimFun: ease-out;

.carousel-flipp-rt {
	&-enter-active {
		animation:  slideRight  $animationSpeed $animTimFun ;
	}
	&-leave-active {
		animation:  slideLeft $animationSpeed $animTimFun ;
	}
}
.carousel-flipp-lf {
	&-enter-active {
		animation:   slideLeft $animationSpeed $animTimFun reverse;
	}
	&-leave-active {
		animation:  slideRight $animationSpeed $animTimFun reverse;
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
</style>
