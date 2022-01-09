<script lang="ts">
	export let color: string;
	export let highlightedColor: string;
	export let click = () => {};
</script>

<button
	class="box"
	style="background: {color};"
	class:highlight={color === highlightedColor}
	aria-label={color}
	on:click={click}
/>

<style>
	.box {
		box-sizing: border-box;
		border: 2px solid #666;
		padding: 0;
		margin: 0;
		transition: background 0.1s ease-out;
	}
	.box:focus-visible {
		box-shadow: 0 0 0 4px blue;
	}
	.highlight,
	.box:active {
		background: black !important;
	}
</style>
