<template>
	<div :style="{ cursor: clickable ? 'pointer' : 'default' }" class="wrapper">
		<input
			:placeholder="namePlaceholder"
			type="text"
			v-if="editableName"
			v-model="store.parties[props.index].name"
		/>
		<div v-else>
			{{ meta?.name || namePlaceholder }}
		</div>
		<div class="row">
			<CharacterCard
				:characterId="meta.members[pos]"
				:clickable="hoverRemove ? Boolean(meta.members[pos]) : true"
				:hoverIntention="hoverRemove && meta.members[pos] ? 'remove' : 'pick'"
				:key="meta.members[pos]"
				@click="emit('cardClick', pos)"
				v-for="pos in [0, 1, 2, 3]"
			/>
		</div>
	</div>
</template>

<script setup>
	import { CharacterCard } from "@/components"
	import { computed } from "vue"
	import { useUserDataStore } from "@/stores"

	const emit = defineEmits(["cardClick"])
	const props = defineProps({
		clickable: Boolean,
		editableName: { default: false, type: Boolean },
		hoverRemove: { default: false, type: Boolean },
		index: Number,
	})

	const store = useUserDataStore()
	const meta = computed(() => store.parties[props.index])
	const namePlaceholder = computed(() => `Party ${1 + props.index}`)
</script>

<style scoped>
	.wrapper {
		display: grid;
		gap: 0.5em;
		justify-content: center;
		text-align: center;
	}

	input {
		border-radius: 1em;
		border: none;
		margin: 0 5em;
		padding: 0.5em;
		text-align: center;
	}

	.row {
		background-color: #fff1;
		border-radius: 0.25em;
		gap: 0.5rem;
		padding: 0.5rem;
	}
</style>
