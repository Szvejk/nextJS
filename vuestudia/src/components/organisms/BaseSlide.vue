<template>
	<ArrowButton @click="changeSlide(-1)" :is-disabled="!isAbleToGoToPrevSlide" />
	<BaseImage :file-name="fileName" :caption-text="captionText" />
	<ArrowButton
		@click="changeSlide(1)"
		:is-disabled="!isAbleToGoToNextSlide"
		is-next
	/>
</template>

<script>
import BaseImage from '@/components/molecules/BaseImage.vue';
import ArrowButton from '@/components/atoms/ArrowButton.vue';
import { computed, ref } from '@vue/reactivity';
import { onMounted, onUnmounted } from '@vue/runtime-core';
import { useRouter } from 'vue-router';
import { getNumberOfImages } from '@/helpers/getNumberOfImages';
export default {
	name: 'BaseSlide',
	components: {
		BaseImage,
		ArrowButton,
	},
	props: {
		photoNumber: {
			type: String,
			default: '1',
		},
	},
	setup(props) {
		const { push } = useRouter();
		const numberOfImages = getNumberOfImages();
		const slideNumber = ref(+props.photoNumber);
		const isAbleToGoToPrevSlide = computed(() => slideNumber.value !== 1);
		const isAbleToGoToNextSlide = computed(
			() => slideNumber.value !== numberOfImages
		);
		onMounted(() => document.addEventListener('keydown', handleKeyDown));
		onUnmounted(() => document.removeEventListener('keydown', handleKeyDown));
		function handleKeyDown({ keyCode }) {
			if (keyCode === 37 && isAbleToGoToPrevSlide.value) {
				changeSlide(-1);
			} else if (keyCode === 39 && isAbleToGoToNextSlide.value) {
				changeSlide(1);
			}
		}
		function changeSlide(param) {
			slideNumber.value = slideNumber.value + param;
			push(`/slide/${slideNumber.value}`);
		}
		const fileName = computed(() => `${slideNumber.value}.jpg`);
		const captionText = computed(
			() => `${slideNumber.value}/${numberOfImages}`
		);
		return {
			isAbleToGoToPrevSlide,
			isAbleToGoToNextSlide,
			fileName,
			captionText,
			changeSlide,
		};
	},
};
</script>

<style lang="scss" scoped></style>