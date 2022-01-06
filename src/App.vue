<template>
<div class="main" :class="{'show-all': mainClass}">
	<transition name="is-animating" appear>
		<div class="counter" :style="{'height': 'calc(100vh - '+headingHeight+'px)'}" :class="{'is-animating': isCounterAnimating}">
			<span>{{currentId}}</span>
		</div>
	</transition>
	<div class="container">
		<div ref="headRef" class="heading" :class="{'is-showing': showInfoBox}">
			<div class="heading-back" :style="{'top': headingHeight+'px'}"></div>
			<div class="heading-title">Женице моја, срећан ти рођендан!!!</div>
			<div class="heading-description">Пошто ниси отишла на Дивчибаре за свој рођендан, био сам у могућности да ти поклоним нешто!</div>
		</div>
		<div class="gift" :style="{'height': 'calc(100vh - '+headingHeight+'px)'}">
			<button class="gift-button gift__button" :class="{'is-hidden': hideButton}" @click="setAnimations">Отвори поклон</button>
			<transition-group name="is-animating" tag="div" @animationend="leaveAnimating" appear>
				<div v-for="box in [1,2,3,4,5,6,7]" :key="box" class="gift-box" :class="['gift-box--level-'+(box), {'is-showing': box === currentId, 'is-hidding': hidePrev && box > currentId, 'is-animating': box === currentId && isAnimating }, classBox]" :style="{'z-index': box}">
					
						<div class="gift-box-top">
							<span></span>
						</div>
					<div class="gift-box-bottom">
						<span></span>
					</div>
					<div class="gift-box-line gift-box-line--vertical"></div>
					<div class="gift-box-line gift-box-line--horizontal"></div>
				</div>
			</transition-group>
		</div>
		<div class="info-box" :style="{'height': 'calc(100vh - '+headingHeight+'px)'}" :class="{'is-showing': showInfoBox || showFinalMessage, 'info-box--final': showFinalMessage}">
			<div class="info-box__content">
				<div v-if="counter !== 0" class="info-box__title">
					Ево га поклон стиже на време, ево само што није. Сачекај још<br><span style="font-style: normal; font-weight: 600; font-size: 40px;">{{counter}}</span> сек.
				</div>
				<div v-else-if="counter === 0 && !showFinalMessage" class="info-box__title">
					Честитам на стрпљењу, можеш да погледаш свој поклон
				</div>
				<div v-else class="info-box__title">
					Слободно се диви још који тренутак овој страници, а онда погледај иза себе :D
				</div>
				<div v-if="counter === 33" class="gift-button info-box__button" @click="startCounter">Почни да бројиш</div>
				<div v-if="counter === 0 && !showFinalMessage" class="gift-button info-box__button" @click="onShowFinalMessage">Погледај поклон</div>
			</div>
		</div>
	</div>
</div>
</template>
<script>
export default {
	name: 'App',
	data() {
		return {
			currentId: 7,
			counter: 33,
			headingHeight: 0,
			hideButton: false,
			hidePrev: false,
			isCounterAnimating: false,
			isAnimating: false,
			mainClass: false,
			showInfoBox: false,
			showFinalMessage: false
		}
	},
	mounted() {
		// console.dir(this.$refs.headRef.clientHeight)
		if (this.$refs.headRef) {
			this.headingHeight = this.$refs.headRef.clientHeight
		}
		window.addEventListener('resize', this.onWindowResize)
	},
	unmounted() {
		window.removeEventListener('resize', this.onWindowResize)
	},
	methods: {
		setAnimations() {
			// hide button
			this.hideButton = true;
			// animate
			this.isCounterAnimating = true;
			this.isAnimating = true;
		},
		resetAnimations() {
			// show button
			this.hideButton = false;
			// stop animate
			this.isCounterAnimating = false;
			this.isAnimating = false;
		},
		leaveAnimating(ev) {
			// console.log('leaveAnimating', ev)
			if (ev.animationName === 'boxAnimation') {
				if (this.currentId === 1) {
					this.showInfoBox = true;
					return;
				}
				this.hidePrev = true;
				this.resetAnimations();
				this.currentId = this.currentId - 1
			}
		},
		startCounter() {
			this.counter --;
			if (this.counter !== 0) {
				setTimeout(() => {
					this.startCounter()
				}, 1000)
			}
		},
		onShowFinalMessage() {
			this.showInfoBox = false;
			setTimeout(() => {
				this.mainClass = true;
				this.showFinalMessage = true;
			}, 500)
		},
		onWindowResize() {
			if (this.$refs.headRef) {
				this.headingHeight = this.$refs.headRef.clientHeight
			}
		}
	}
}
</script>
<style lang="scss">
@import './assets/scss/colors.scss';
@import './assets/scss/mixins.scss';
@import url('https://cdn.jsdelivr.net/npm/reset-css@4.0.1/reset.min.css');
@import url('https://fonts.googleapis.com/css2?family=Raleway:ital,wght@0,400;0,600;1,200&display=swap');
body {
	font-family: 'Raleway', sans-serif;
}
.main {
	position: relative;
	width: 100%;
	height: 100vh;
	overflow: hidden;
	&::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-image: url('./assets/img/background.jpg');
		background-size: cover;
		background-position: 40%;
		z-index: -1;
		filter: blur(4px) sepia(1);
	}
	&.show-all {
		&::before {
			filter: none;
		}
	}

	@include tablet-up {
		&::before {
			background-position: center;
		}
	}
}
.counter {
	position: absolute;
	width: 100%;
	height: 100%;
	bottom: 0;
	left: 0;
	display: flex;
	align-items: center;
	justify-content: center;
	z-index: 1000;
	font-size: 400px;
	visibility: hidden;
	opacity: 0;
	span {
		color: $secondaryDark;
	}

	&.is-animating {
		animation-duration: 1s;
		animation-delay: 0.2s;
		animation-name: zoomOut;
	}
}
.container {
	width: 100%;
	max-width: 960px;
	margin: 0 auto;
	padding: 0 16px;
	box-sizing: border-box;
}
.heading {
	padding: 16px;
	text-align: center;
	line-height: 24px;
	color: $secondaryDark;
	background-color: rgba($color: #fff, $alpha: 0.7);
	border-radius: 0 0 4px 4px;
	transition: all 0.5s ease;
	@include tablet-up {
		padding: 32px 16px;
		line-height: 32px;
	}

	&.is-showing {
		border-radius: 0;
		.heading-back {
			height: 100vh;
		}
	}
}

.heading-back {
	display: block;
	position: absolute;
	left: 16px;
	top: 0;
	background-color: rgba(255, 255, 255, 0.7);
	width: calc(100% - 32px);
	height: 0;
	transition: all 0.5s ease;
}

.heading-title {
	font-size: 32px;
	font-weight: 600;
	line-height: 40px;
	@include tablet-up {
		font-size: 48px;
		line-height: 56px;
	}
}

.heading-description {
	margin-top: 16px;
	font-style: italic;
}

.info-box {
	position: absolute;
	z-index: 1000;
	width: 100%;
	height: 100%;
	left: 0;
	bottom: 0;
	display: flex;
	align-items: flex-end;
	justify-content: center;
	visibility: hidden;
	opacity: 0;
	transition: all 0.2s ease-in;

	&.is-showing {
		visibility: visible;
		opacity: 1;
	}

	.gift-button {
		max-width: 200px;
    justify-self: center;
	}
}

.info-box--final {
	.info-box__title {
		color: $secondaryLight;
    font-style: inherit;
    font-weight: 600;
    text-shadow: 2px 2px black;
    font-size: 40px;
		line-height: 48px;
	}
}

.info-box__content {
	padding: 16px;
	width: calc(100% - 32px);
	height: 100%;
	display: grid;
	justify-content: center;
	align-content: center;
	border-radius: 4px;
	box-sizing: border-box;
}

.info-box__title {
	font-size: 32px;
	line-height: 40px;
	text-align: center;
	font-style: italic;
	color: $secondaryDark;
}

.info-box__button {
	margin-top: 32px;
}

.gift {
	position: relative;
}

.gift__button {
	position: absolute;
	top: calc(50% - 17px);
	left: calc(50% - 75px);
}

.gift-button {
    display: inline-flex;
    justify-content: center;
    padding: 8px 16px;
    background-color: $primaryLight;
    color: $primaryDark;
    line-height: 16px;
    text-transform: uppercase;
    font-weight: 600;
    border-radius: 4px;
    border: 1px solid $primaryDark;
    min-width: 150px;
    z-index: 100;
    cursor: pointer;
		opacity: 1;
		transition: opacity 0.2s ease-out;
		&.is-hidden {
			opacity: 0;
		}
}

.gift-box {
	position: absolute;
	width: 100%;
	height: 320px;
	// visibility: hidden;
	// opacity: 0;
	transition: all 0.2s ease-in;

	&.is-showing {
		visibility: visible;
		opacity: 1;
	}

	&.is-hidding {
		visibility: hidden;
		opacity: 0;
	}

	&.is-animating {
		animation-name: boxAnimation;
		animation-duration: 2s;
		animation-delay: 1.7s;
		animation-fill-mode: forwards;
		.gift-box-top {
			animation-name: topBoxAnimation;
			animation-duration: 1s;
			animation-delay: 1.2s;
			animation-fill-mode: forwards;
		}
		.gift-box-line--vertical {
			animation-name: lineVerticalAnimation;
			animation-duration: 1.2s;
			animation-delay: 1.2s;
			animation-fill-mode: forwards;
		}
	}
}

@keyframes boxAnimation {
	0% {
		transform: translate(0) rotate(0deg);
		opacity: 1;
	}
	50% {
		transform: translate(0) rotate(0deg);
		opacity: 1;
	}
	80% {
		// transform: translate(-30%, 50%) rotate(4deg);
		opacity: 1;
	}
	100% {
		transform: translate(-75%, 150%) rotate(16deg);
		opacity: 0;
	}
}

@keyframes topBoxAnimation {
	0% {
		transform: rotateZ(0) translate(0);
		opacity: 1;
	}
	60% {
		transform: rotateZ(135deg) translate(-10%, -20%);
		opacity: 1;
	}
	100% {
		transform: rotateZ(135deg) translate(-100%, -200%);
		opacity: 0;
	}
}

@keyframes lineVerticalAnimation {
	0% {
		transform: scaleY(1) skewX(0deg);
	}
	60% {
		transform: scaleY(-1) skewX(16deg);
	}
	100% {
		transform: scaleY(0) skewX(0deg);
	}
}

.gift-box--level-7 {
	width: calc(100% - 32px);
	height: 80%;
	top: 10%;
	left: 16px;
}
.gift-box--level-6 {
	width: calc(100% - 80px);
	height: 70%;
	top: 15%;
	left: 40px;
}
.gift-box--level-5 {
	width: calc(100% - 112px);
	height: 60%;
	top: 20%;
	left: 56px;
}
.gift-box--level-4 {
	width: calc(100% - 144px);
	height: 50%;
	top: 25%;
	left: 72px;
}
.gift-box--level-3 {
	width: calc(100% - 176px);
	height: 40%;
	top: 30%;
	left: 88px;
}
.gift-box--level-2 {
	width: calc(100% - 208px);
	height: 30%;
	top: 35%;
	left: 104px;
}
.gift-box--level-1 {
	width: calc(100% - 240px);
	height: 20%;
	top: 40%;
	left: 120px;
}
.gift-box-top {
	position: relative;
	border: 1px solid $primaryDark;
	height: 10%;
	background-color: $secondaryLight;
	border-radius: 4px;
	transform: rotateZ(0) translate(0);
	transform-origin: right center;
	opacity: 1;
	
	span {
		position: absolute;
		width: 100%;
		height: 100%;
		background-color: $primaryLight;
		opacity: 0.5;
		background: repeating-linear-gradient( -45deg, $secondaryDark, $secondaryDark 3%, $primaryLight 3%, $primaryLight 8% );
		border-radius: 4px;
		@include tablet-up {
			background: repeating-linear-gradient(-45deg, $secondaryDark, $secondaryDark 4px, $primaryLight 4px, $primaryLight 20px );
		}
	}
}
.gift-box-bottom {
	position: relative;
	margin: 0 auto;
	width: calc(100% - 32px);
	height: calc(100% - (10% + 2px));
	background-color: $secondaryLight;
	border: 1px solid $primaryDark;
	border-top: none;
	overflow: hidden;
	border-radius: 0 0 4px 4px;

	span {
		width: 100%;
		height: 100%;
		position: absolute;
		background-color: $primaryLight;
		opacity: 0.5;
		background-image:  linear-gradient(135deg, $secondaryDark 25%, transparent 25%), linear-gradient(225deg, $secondaryDark 25%, transparent 25%), linear-gradient(45deg, $secondaryDark 25%, transparent 25%), linear-gradient(315deg, $secondaryDark 25%, $primaryLight 25%);
		background-position: 3% 0, 3% 0, 0 0, 0 0;
		background-size: 6% 6%;
		background-repeat: repeat;
		@include tablet-up {
			background-position: 9px 0, 9px 0, 0 0, 0 0;
			background-size: 18px 18px;
		}
	}
}
.gift-box-line {
	position: absolute;
	width: 10%;
	height: 100%;
	top: 0;
	left: calc(50% - 5%);
	background-color: $primaryDark;
}
.gift-box-line--vertical {
	transform: scaleY(1) skewX(0deg);
	transform-origin: bottom;
}
.gift-box-line--horizontal {
	width: calc(100% - 16px);
	height: 10%;
	top: calc(50% - 5%);
	left: 8px;
	border-radius: 0 0 4px 4px;

	&::before,
	&::after {
		content: '';
		position: absolute;
		border-width: 6px 8px 6px 0;
		border-color: transparent #4f3d6a transparent transparent;
		border-style: solid;
		top: -6px;
		z-index: -1;
	}
	&::after {
		right: 0;
		border-width: 0 8px 6px 0;
		border-color: transparent transparent #4f3d6a transparent;
	}
}

// ANIMATION
@keyframes zoomOut {
	from {
		font-size: 400px;
		visibility: visible;
		opacity: 1;
	}
	to {
		font-size: 0;
		visibility: visible;
		opacity: 1;
	}
}

</style>