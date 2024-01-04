<script lang="ts">
	import { nuts } from '../stores';

	let pressingSources: Map<string, Set<string>> = new Map();

	function updateSources() {
		pressingSources = pressingSources; // -_- https://svelte.dev/repl/e37be0fa87844cc18f1064b711d427ad?version=3.29.7
	}

	function onKeyDown(e: KeyboardEvent) {
		if (e.key != ' ') return;
		pressNut('key', e.key);
	}

	function onKeyUp(e: KeyboardEvent) {
		if (e.key != ' ') return;
		releaseNut('key', e.key);
	}

	function pressNut(source: string, key: string = 'default') {
		let set = pressingSources.get(source);

		// don't re-press
		if (set && set.has(key)) return;

		// store press
		pressingSources.set(source, (set ?? new Set()).add(key));
		updateSources();

		// add nuts
		$nuts++;
	}

	function releaseNut(source: string, key: string = 'default') {
		const sourceSet = pressingSources.get(source);

		// make sure it's pressed
		if (!sourceSet || !sourceSet.has(key)) return;

		// remove key from source set
		sourceSet.delete(key);

		// remove source set if no keys
		if (sourceSet.size == 0) pressingSources.delete(source);
		updateSources();
	}

	let zoom = 1;

	$: {
		const zoomDecay = 0.5;
		let extraZoom = 0.2;
		zoom = 1;

		const presses = Array.from(pressingSources, ([key, value]) =>
			value instanceof Set ? Array.from(value) : value
		).flat();

		for (const _ of presses) {
			zoom -= extraZoom;
			extraZoom *= zoomDecay;
		}
	}
</script>

<svelte:window on:keydown={onKeyDown} on:keyup={onKeyUp} />

<div class="flex flex-col items-center justify-center">
	<div class="text-3xl">
		{Math.round($nuts * 1000) / 1000} nuts
	</div>
	<div
		class="inline-block h-40 w-40 select-none
		bg-[url('/nut.png')] bg-contain bg-no-repeat bg-center
		transition-transform"
		style:transform={`scale(${zoom})`}
		on:mousedown={(e) => pressNut('mouse', e.button.toString())}
		on:mouseup={(e) => releaseNut('mouse', e.button.toString())}
		on:mouseleave={(e) => releaseNut('mouse')}
		on:contextmenu={(e) => {
			e.preventDefault();
		}}
		role="button"
		tabindex={null}
	></div>
</div>
