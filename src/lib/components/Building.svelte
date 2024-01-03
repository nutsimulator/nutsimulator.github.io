<svelte:options accessors />

<script lang="ts">
	import { nuts } from '../stores';

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
	<button
		on:click={buy}
		class="py-3 px-5 rounded-lg cursor-pointer select-none [box-shadow:0_5px_#999]
        text-white
        transition-all duration-75
        hover:brightness-90
        active:translate-y-[4px] active:[box-shadow:0_1px_#666] active:brightness-75
        {$nuts < price && '!brightness-75'}"
		style:background-color={colour}
	>
		<div class="font-bold">{name} ({count})</div>
		<div>(+{nutIncrement} nuts/sec)</div>
		<div>-{price.toPrecision(2)} nuts</div>
	</button>
{/if}
