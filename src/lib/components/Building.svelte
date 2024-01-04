<svelte:options accessors />

<script lang="ts">
	import { nuts } from '../stores';
	import Button from './Button.svelte';

	export let name: string;
	export let nutIncrement: number;
	export let price: number;
	export let priceMultiplier: number;
	export let colour: string;
	export let count: number;

	const basePrice = price;
	let unlocked = false;

	nuts.subscribe((value) => {
		if (value > price) unlocked = true;
	});

	function buy() {
		if ($nuts < price) return;

		$nuts -= price;

		// raise price
		price = Math.floor(basePrice * Math.pow(priceMultiplier, ++count));
	}
</script>

{#if unlocked}
	<Button onClick={buy} disabled={$nuts < price} {colour}>
		<div class="font-bold">{name} ({count})</div>
		<div>(+{nutIncrement} nuts/sec)</div>
		<div>-{price} nuts</div>
	</Button>
{/if}
