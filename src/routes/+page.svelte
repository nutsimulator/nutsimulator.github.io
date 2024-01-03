<script lang="ts">
	import { onMount } from 'svelte';

	import Building from '$lib/components/Building.svelte';
	import ClickableNut from '../lib/components/ClickableNut.svelte';

	import { nuts } from '$lib/stores';
	import game_data from '$lib/game_data.json';

	type BuildingMap = {
		[name: string]: Building;
	};
	const buildingElems: BuildingMap = {};

	function getPassiveNutIncrement() {
		return Object.values(buildingElems).reduce(
			(sum, building) => sum + building.nutIncrement * building.count,
			0
		);
	}

	onMount(() => {
		const loop = setInterval(() => {
			$nuts += getPassiveNutIncrement();
		}, game_data.tickrate * 1000);

		return () => {
			clearInterval(loop);
		};
	});
</script>

<div class="p-5 grid grid-rows-[1fr_auto-1fr] h-full">
	<div class="text-center">
		<div class="grid h-full">
			<div>
				<div>
					<h1>nut simulator</h1>
				</div>

				<div>
					<div class="text-3xl">Level 1</div>
				</div>
			</div>
		</div>
	</div>

	<div class="grid grid-cols-3">
		<div>Items</div>

		<ClickableNut />

		<div class="text-right">Upgrades</div>
	</div>

	<div class="flex justify-center flex-col">
		<div class="flex flex-wrap justify-center gap-2">
			{#each game_data.buildings as building}
				<Building
					bind:this={buildingElems[building.name]}
					name={building.name}
					nutIncrement={building.nutIncrement}
					price={building.price}
					priceMultiplier={building.priceMultiplier}
					colour={building.colour}
					count={0}
				/>
			{/each}
		</div>
	</div>
</div>
