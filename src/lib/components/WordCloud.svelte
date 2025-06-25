<script lang="ts">
	import type { Word } from 'd3-cloud';

	import cloud from 'd3-cloud';
	import { select } from 'd3-selection';
	import { scaleLinear } from 'd3-scale';
	import { transition } from 'd3-transition';
	import { onMount } from 'svelte';
	import { page } from '$app/state';

	interface Props {
		words: string[];
	}

	let svgContainer: SVGElement | null = null;

	let width = 800;
	let height = 800;
	let minAppearances = 2;

	let { words }: Props = $props();

	onMount(() => {
		const { searchParams } = page.url;
		const interval = parseInt(searchParams.get('interval') || '1000');
		width = parseInt(searchParams.get('width') || `${width}`);
		height = parseInt(searchParams.get('height') || `${height}`);
		minAppearances = parseInt(searchParams.get('min') || `${minAppearances}`);

		const intervalId = window.setInterval(() => {
			if (!svgContainer) return;

			const countedWords = countWords(words).filter(
				(currentWord) => currentWord.value >= minAppearances
			);

			drawCloud(countedWords);
		}, interval);

		return () => {
			window.clearInterval(intervalId);
		};
	});

	const sizeScale = scaleLinear().domain([0, 1]).range([10, 100]);

	const countWords = (wordsToCount: string[]) => {
		const wordCount: Record<string, number> = {};

		wordsToCount.forEach((word) => {
			wordCount[word] = (wordCount[word] || 0) + 1;
		});

		return Object.entries(wordCount).map(([text, value]) => ({ text, value }));
	};

	const drawCloud = (countedWords: { text: string; value: number }[]) => {
		const values = countedWords.map((word) => word.value);
		sizeScale.domain([Math.min(...values), Math.max(...values)]);

		// configure layout
		const layout = cloud()
			.size([width, height])
			.words(countedWords.map((d) => ({ ...d, size: sizeScale(d.value) })))
			.padding(5)
			.font('Impact')
			.rotate(0)
			.fontSize((d) => d.size || 10)
			.random(() => 0.5)
			.on('end', draw);

		layout.start();
	};

	const draw = (placedWords: Word[]) => {
		if (!svgContainer) return;

		const t = transition().duration(800);

		// select and bind by key (word text)
		const sel = select(svgContainer)
			.attr('width', width)
			.attr('height', height)
			.selectAll<SVGTextElement, Word>('text')
			.data(placedWords, (d) => d.text || '');

		sel.exit().transition(t).style('opacity', 0).remove();

		const enter = sel
			.enter()
			.append('text')
			.attr('text-anchor', 'middle')
			.attr('transform', `translate(${width / 2},${height / 2})`)
			.style('font-family', 'Impact')
			.style('fill-opacity', 0)
			.style('font-size', '1px')
			.text((d) => d.text || '');

		enter
			.merge(sel)
			.transition(t)
			.style('fill-opacity', 1)
			.style('font-size', (d) => `${d.size}px`)
			.attr(
				'transform',
				(d) => `translate(${(d.x || 0) + width / 2},${(d.y || 0) + height / 2})rotate(${d.rotate})`
			);
	};
</script>

<svg bind:this={svgContainer} class="bg-red-100 transition-all" />
