<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { browser } from '$app/env';

	import { goto } from '$app/navigation';
	import { onMount } from 'svelte';
	let colorsExpected: string[] = [];
	let colorsDid: string[] = [];
	onMount(() => {
		if (browser) {
			const params = new URLSearchParams(window.location.search);
			colorsExpected = params.get('pattern').split(',') || [];
			colorsDid = params.get('completed').split(',') || [];
		}
	});
</script>

<svelte:head>
	<title>Game Over</title>
</svelte:head>

<button on:click={() => goto('/play')}>Play again</button>

{#if colorsExpected.length >= 1 && colorsDid.length >= 1}
	<h2>You completed {colorsExpected.length - 1} levels</h2>

	<p>Pattern expected</p>

	<div class="color-container">
		{#each colorsExpected as color}
			<span class="box" style={`background: ${color}`} />
		{/each}
	</div>

	<p>What you did</p>

	<div class="color-container">
		{#each colorsDid as color}
			<span class="box" style={`background: ${color}`} />
		{/each}
	</div>
{/if}

<style>
	.color-container {
		margin: 0 -16px;
		white-space: nowrap;
		overflow-x: scroll;
		padding: 0 16px 8px;
	}
	.color-container::-webkit-scrollbar-track {
		display: none;
	}
	.box {
		display: inline-block;
		width: 18px;
		height: 18px;
		border: 1px solid #000;
		margin-right: 8px;
	}
	.box:last-child {
		margin-right: 0;
	}
</style>
