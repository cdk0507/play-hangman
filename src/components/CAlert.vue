@ -1,46 +0,0 @@
<script lang="ts">
export default {
	props: ["params"],
	data() {
		return {
			text: this.params.text,
		};
	},
	methods: {
		confirm() {
			this.$emit("confirm", true);
		},
	},
};
</script>

<template>
	<div class="bg">
		<div class="content">
			<p>{{ text }}</p>
			<button @click="confirm">확인</button>
		</div>
	</div>
</template>

<style scoped lang="scss">
.bg {
	background-color: rgba(100, 100, 100, 0.3);
	position: fixed;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	display: flex;
	justify-content: center;
	align-items: center;
	z-index: 999999;
}
.content {
	max-width: 300px;
	@apply w-full rounded bg-white px-3 py-5 text-center;
}
button {
	background-color: $color-point;
	@apply text-white rounded-sm px-4 py-2 mt-5;
}
</style>
