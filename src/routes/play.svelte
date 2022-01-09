<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { goto } from '$app/navigation';
	import GameButton from '$lib/GameButton.svelte';
	import { onMount } from 'svelte';

	const colorOptions = {
		beige: null,
		green: null,
		blue: null,
		red: null,
		yellow: null,
		purple: null
	};
	const colorOptionsNames = Object.keys(colorOptions);
	const lengthOfTimeBetweenColors = 400;

	let isDemonstratingOrder = true;
	let order: string[] = [];
	let currentColor: string = null;
	let clickOrder: string[] = [];

	onMount(() => {
		addNextColorOption();

		colorOptionsNames.forEach((color) => {
			colorOptions[color] = document.getElementById(color);
		});

		setTimeout(() => {
			displayOrder();
		}, 500);
	});

	function displayOrder() {
		order.forEach((color, i) => {
			const timeSeparation = lengthOfTimeBetweenColors * (i + 1);
			setTimeout(() => {
				// find button and click it programatically
				colorOptions[color].classList.add('highlight');
				setTimeout(() => {
					colorOptions[color].classList.remove('highlight');
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
			clickOrder = [];
			addNextColorOption();
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

	function getRandomFromOptionsList() {
		return Math.floor(Math.random() * colorOptionsNames.length);
	}
	function addNextColorOption() {
		const nextColor = colorOptionsNames[getRandomFromOptionsList()];
		const prevColor = order[order.length - 1];
		const secondToLastPrevColor = order[order.length - 2];
		if (order.length <= 2 || (prevColor !== nextColor && secondToLastPrevColor !== nextColor)) {
			order = [...order, nextColor];
		} else {
			addNextColorOption();
		}
	}
</script>

<svelte:head>
	<title>Play</title>
</svelte:head>

<p>Current Level {order.length}</p>

<section class:disabled={isDemonstratingOrder}>
	{#each colorOptionsNames as color, i}
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
