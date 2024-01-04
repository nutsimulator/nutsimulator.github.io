<script lang="ts">
	export let disabled: boolean;
	export let colour: string;
	export let onClick: () => void;
</script>

<button
	on:click={onClick}
	class="py-3 px-5 rounded-lg cursor-pointer select-none [box-shadow:0_5px_#999]
        text-white
        transition-all duration-75
        hover:brightness-90
        active:translate-y-[4px] active:[box-shadow:0_1px_#666] active:brightness-75
        {disabled && '!brightness-75'}"
	style:background-color={colour}
>
	<slot />
</button>
