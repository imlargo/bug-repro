<script lang="ts">
	import * as Select from '$lib/components/ui/select/index.js';

	const fruits = Array.from({ length: 200 }, (_, i) => ({
		value: `fruit-${i}`,
		label: `Fruit ${i}: Organically Grown Sun-Ripened Premium Reserve Heirloom Cultivar Hand-Picked at Dawn from the Terraced Highland Orchards of Northern Patagonia`
	}));

	let value = $state('');

	const triggerContent = $derived(fruits.find((f) => f.value === value)?.label ?? 'Select a fruit');
</script>

<Select.Root type="single" bind:value>
	<Select.Trigger class="w-[180px]">
		{triggerContent}
	</Select.Trigger>
	<Select.Content>
		{#each fruits as fruit (fruit.value)}
			<Select.Item value={fruit.value} label={fruit.label} />
		{/each}
	</Select.Content>
</Select.Root>
