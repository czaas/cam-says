<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { onMount } from 'svelte';
	import GameButton from '$lib/GameButton.svelte';
	import { goto } from '$app/navigation';

	const options = ['green', 'red', 'yellow', 'blue'];
	const lengthOfTimeToDisplay = 600;
	const lengthOfTimeBetweenColors = 500;

	let isDemonstratingOrder = true;
	let order: string[] = [options[getNextRandom()]];
	let currentColor: string = null;
	let clickOrder: string[] = [];

	onMount(() => {
		setTimeout(() => {
			displayOrder();
		}, 1000);
	});

	function displayOrder() {
		order.forEach((color, i) => {
			setTimeout(
				() => {
					currentColor = color;
					const lastItem = i === order.length - 1;
					if (lastItem) {
						isDemonstratingOrder = false;
					}
					setTimeout(() => {
						currentColor = null;
					}, lengthOfTimeToDisplay);
				},
				i === 0 ? lengthOfTimeToDisplay - lengthOfTimeBetweenColors : lengthOfTimeBetweenColors * i
			);
		});
	}

	function handleColorClick(color: string) {
		if (isDemonstratingOrder) {
			return;
		}

		const isDoneWithLevel = clickOrder.length + 1 === order.length;
		const expected = order[clickOrder.length];
		const isSameAsLast = color === expected;

		if (isDoneWithLevel && isSameAsLast) {
			const nextColorIndex = getNextRandom();
			clickOrder = [];
			order = [...order, options[nextColorIndex]];
			isDemonstratingOrder = true;
			setTimeout(() => {
				displayOrder();
			}, lengthOfTimeBetweenColors);
		} else if (!isSameAsLast) {
			goto(`/game-over?pattern=${order.join(',')}&completed=${[...clickOrder, color].join(',')}`);
		} else {
			clickOrder = [...clickOrder, color];
		}
	}

	function getNextRandom() {
		return Math.floor(Math.random() * options.length);
	}
</script>

<svelte:head>
	<title>Play</title>
</svelte:head>

<p>Current Level {order.length}</p>

<section class:disabled={isDemonstratingOrder}>
	{#each options as color}
		<GameButton {color} highlightedColor={currentColor} click={() => handleColorClick(color)} />
	{/each}
</section>

<style>
	section {
		display: grid;
		width: 100%;
		grid-template-columns: 50% 50%;
		flex: 1;
		margin: 0 auto;
		grid-gap: 8px;
		box-sizing: border-box;
	}
	.disabled {
		pointer-events: none;
	}
</style>
