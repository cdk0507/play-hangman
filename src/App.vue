<script setup lang="ts">
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
	<header>
		<img
			alt="Vue logo"
			class="logo"
			src="@/assets/logo.svg"
			width="125"
			height="125"
		/>
		<nav>
			<RouterLink to="/">Home</RouterLink>
			<RouterLink to="/about">About</RouterLink>
			<RouterLink to="/hangman">행맨</RouterLink>
		</nav>
	</header>

	<RouterView />
</template>

<style scoped lang="scss">
header {
	display: flex;
	align-items: center;
	justify-content: center;
	img {
		width: 30px;
		height: 30px;
	}
}
</style>
