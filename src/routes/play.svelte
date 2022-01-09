<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { goto } from '$app/navigation';
	import GameButton from '$lib/GameButton.svelte';
	import { onMount } from 'svelte';

	const options = ['green', 'red', 'yellow', 'blue'];
	const lengthOfTimeToDisplay = 600;
	const lengthOfTimeBetweenColors = 400;

	let isDemonstratingOrder = true;
	let order: string[] = [options[getNextRandom()]];
	let currentColor: string = null;
	let clickOrder: string[] = [];

	const el = {
		green: null,
		blue: null,
		red: null,
		yellow: null
	};

	onMount(() => {
		el.green = document.getElementById('green');
		el.blue = document.getElementById('blue');
		el.red = document.getElementById('red');
		el.yellow = document.getElementById('yellow');

		setTimeout(() => {
			displayOrder();
		}, 500);
	});

	function displayOrder() {
		order.forEach((color, i) => {
			const timeSeparation = lengthOfTimeBetweenColors * (i + 1);
			setTimeout(() => {
				// find button and click it programatically
				el[color].classList.add('highlight');
				setTimeout(() => {
					el[color].classList.remove('highlight');
				}, 100);
				if (i === order.length - 1) {
					isDemonstratingOrder = false;
				}
			}, timeSeparation);
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
	{#each options as color, i}
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
