<template>
	<figure :class="{ owned }" :style="{ cursor: clickable ? 'pointer' : 'default' }">
		<picture class="portrait">
			<source :key="src.mime" :srcSet="src.path" :type="src.mime" v-for="src in srcList" />
			<img :src="srcList.at(-1)?.path" :alt="meta?.name || 'Character placeholder'" />
		</picture>
		<picture class="bg" :class="meta?.background ?? 'grey'" v-if="jsonData.assets?.portraitBg.length">
			<source :key="src.type" :srcSet="src.src" :type="src.type" v-for="src in jsonData.assets?.portraitBg" />
			<img :src="jsonData.assets?.portraitBg.at(-1)?.src" alt="" />
		</picture>
		<div class="removeOverlay" v-if="'remove' === hoverIntention">×</div>
		<ElementBadge v-if="meta" :elementId="meta.element" />
		<ConstellationBadge :characterId="characterId" />
		<figcaption>
			{{ meta?.name || props.namePlaceholder || "Empty" }}
		</figcaption>
	</figure>
</template>

<script setup lang="ts">
	import { computed } from "vue"
	import { ConstellationBadge, ElementBadge } from "@/components"
	import { useJsonDataStore } from "@/stores"

	const jsonData = useJsonDataStore()
	const props = defineProps({
		characterId: { required: false, type: String },
		clickable: { default: true, type: Boolean },
		hoverIntention: { default: "pick", type: String },
		namePlaceholder: String,
		owned: { default: true, type: Boolean },
	})

	const meta = computed(() => (
		props.characterId
			? jsonData.characters[props.characterId]
			: undefined
	))
	const srcList = computed(() => {
		const path =
			process.env.VUE_APP_ASSETS_ENDPOINT +
			"portraits/" +
			encodeURIComponent(meta.value?.name ?? "undefined")

		return [
			{ path: path + ".webp", mime: "image/webp" },
			{ path: path + ".png", mime: "image/png" },
		]
	})
</script>

<style scoped lang="scss">
	figure {
		--outside-border-radius: 5%;

		align-items: center;
		background-color: var(--button-background-color);
		border-radius: var(--outside-border-radius);
		color: var(--button-font-color);
		display: flex;
		flex-direction: column;
		margin: 0;
		position: relative;

		&:not(.owned) {
			filter: saturate(0.25) brightness(0.75);
		}

		* {
			user-select: none;
		}

		figcaption {
			font: 0.7rem Hoyofont;
			padding: 0.2rem 0;
		}

		picture.portrait,
		picture.bg {
			background-size: cover;
			border-radius: var(--outside-border-radius) var(--outside-border-radius) 1rem 0;
			height: 5rem;
			width: 5rem;

			img {
				border-radius: inherit;
				height: inherit;
				width: inherit;
			}
		}

		picture.bg {
			position: absolute;

			img {
				object-fit: cover;
			}

			&.grey {
				background-color: #838383;
			}

			&.red {
				background-color: #a54845;
			}

			&.purple {
				background-color: #8d72ba;
			}

			&.yellow {
				background-color: #ab7547;
			}
		}

		picture.portrait {
			z-index: 1;
		}

		.removeOverlay {
			align-items: center;
			background-color: #0005;
			color: #fff;
			display: flex;
			font-size: 3em;
			inset: 0;
			justify-content: center;
			opacity: 0;
			position: absolute;
			transition-duration: 0.2s;
			z-index: 2;
		}

		&:hover .removeOverlay {
			opacity: 1;
		}
	}
</style>
