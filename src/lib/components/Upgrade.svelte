<svelte:options accessors />

<script lang="ts">
	import { nuts } from '../stores';
	import Building from './Building.svelte';
	import Button from './Button.svelte';

	export let name: string;
	export let price: number;
	export let priceMultiplier: number;
	export let requiredBuilding: Building;
	export let colour: string;
	export let count: number;
	export let maxCount: number;

	const basePrice = price;
	let unlocked = false;

	nuts.subscribe((value) => {
		if (requiredBuilding?.count > 0) unlocked = true;
	});

	function buy() {
		if ($nuts < price) return;
		if (count >= maxCount) return;

		$nuts -= price;

		// raise price
		price = Math.floor(basePrice * Math.pow(priceMultiplier, ++count));

		requiredBuilding.nutIncrement *= 2;
	}
</script>

{#if unlocked}
	<Button onClick={buy} disabled={$nuts < price} {colour}>
		<div class="font-bold">{name} ({count})</div>
		<div>({requiredBuilding.name} * 2 nuts/sec)</div>
		<div>-{price} nuts</div>
	</Button>
{/if}
