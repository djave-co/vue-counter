<template>
	<span v-observe-visibility="{ callback: visibilityChanged, intersection: { threshold: 0 }}">
		<!--
			Make sure not to keep these spans next to each 
			other or spaces will appear like this £ 10 k
		-->
		<span
			class="counter--prefix" v-if="prefix && prefix.length" v-html="prefix"></span><span
			class="counter--amount" v-text="current"></span><span
			class="counter--suffix" v-if="suffix && suffix.length" v-html="suffix"></span>
	</span>
</template>

<script>
	export default {
		props: {
			stat: { type: Number, default: null },
			prefix: { type: String, default: null },
			suffix: { type: String, default: null },
			decimals: { type: Number, default: 0 },
		},
		data(){
			return {
				from: 0,
				to: this.stat,
				animating: false,
				value: 0,
			}
		},
		computed: {
			current(){
				return (this.easing(this.value) * this.stat).toFixed(this.decimals);
			}
		},
		methods: {
			// Taken from here:
			// https://gist.github.com/gre/1650294
			easing(t){
				return t<.5 ? 4*t*t*t : (t-1)*(2*t-2)*(2*t-2)+1;
			},
			animate(){
				if(this.value < 1){
					this.value += 0.01;
					return requestAnimationFrame(this.animate);
				}
				this.value = 1;
			},
			// Uses vue-observe-visability
			// https://github.com/Akryum/vue-observe-visibility
			visibilityChanged (isVisible, entry) {
				if(isVisible){
					this.animate();
				}else{
					if(!entry.isIntersecting){
						this.value = 0;
					}
				}
			}
		}
	}
</script>
