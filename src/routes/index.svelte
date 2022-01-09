<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { goto } from '$app/navigation';
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<p>You've played it before; follow the color patterns and don't mess up! Good luck</p>

<button on:click={() => goto('/play')}>Play</button>
