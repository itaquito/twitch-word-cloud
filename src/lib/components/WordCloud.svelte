<script lang="ts">
	import type { Word } from 'd3-cloud';

	import cloud from 'd3-cloud';
	import { select } from 'd3-selection';
	import { scaleLinear } from 'd3-scale';
	import { transition } from 'd3-transition';
	import { onMount } from 'svelte';

	interface Props {
		words: string[];
		width?: number;
		height?: number;
	}

	let svgContainer: SVGElement | null = null;

	let { words, width = 800, height = 800 }: Props = $props();

	onMount(() => {
		const interval = window.setInterval(() => {
			if (svgContainer) {
				drawCloud(words);
			}
		}, 1000); // Redraw every 5 seconds

		return () => {
			window.clearInterval(interval);
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

	const drawCloud = (newWords: string[]) => {
		const countedWords = countWords(newWords);

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
			.selectAll<SVGElement, Word>('text')
			.data(placedWords, (d) => d.text);

		// EXIT
		sel.exit().transition(t).style('opacity', 0).remove();

		// ENTER
		const enter = sel
			.enter()
			.append('text')
			.attr('text-anchor', 'middle')
			.attr('transform', `translate(${width / 2},${height / 2})`) // start in center
			.style('font-family', 'Impact')
			.style('fill-opacity', 0)
			.style('font-size', '1px') // tiny to grow from
			.text((d) => d.text);

		// ENTER + UPDATE
		enter
			.merge(sel)
			.transition(t)
			.style('fill-opacity', 1)
			.style('font-size', (d) => `${d.size}px`)
			.attr(
				'transform',
				(d) => `translate(${d.x + width / 2},${d.y + height / 2})rotate(${d.rotate})`
			);
	};
</script>

<svg bind:this={svgContainer} class="transition-all" />
