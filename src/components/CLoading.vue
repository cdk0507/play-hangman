<script lang="ts">
export default {
	props: ["params"],
};
</script>

<template>
	<div class="bg">
		<div class="content">
			<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
				<path
					fill="hsla(160, 100%, 37%, 1)"
					d="M304 48a48 48 0 1 0 -96 0 48 48 0 1 0 96 0zm0 416a48 48 0 1 0 -96 0 48 48 0 1 0 96 0zM48 304a48 48 0 1 0 0-96 48 48 0 1 0 0 96zm464-48a48 48 0 1 0 -96 0 48 48 0 1 0 96 0zM142.9 437A48 48 0 1 0 75 369.1 48 48 0 1 0 142.9 437zm0-294.2A48 48 0 1 0 75 75a48 48 0 1 0 67.9 67.9zM369.1 437A48 48 0 1 0 437 369.1 48 48 0 1 0 369.1 437z"
				/>
			</svg>
		</div>
	</div>
</template>

<style scoped lang="scss">
.bg {
	background-color: #fff;
	position: fixed;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	display: flex;
	justify-content: center;
	align-items: center;
	z-index: 99999;
}
.content {
	svg {
		width: 30px;
		@apply animate-spin;
	}
}
</style>
