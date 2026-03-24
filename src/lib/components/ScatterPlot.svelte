<script>
	import * as d3 from 'd3';

	export let data = [];

	let svg;
	let tooltip;

	$: if (svg && data.length > 0) {
		drawChart();
	}

	function drawChart() {
		const margin = { top: 20, right: 20, bottom: 60, left: 65 };
		const width = 540 - margin.left - margin.right;
		const height = 350 - margin.top - margin.bottom;

		const cleanData = data.filter((d) => d.Year && d.Global_Sales && d.Critic_Score);

		const svgEl = d3.select(svg);
		svgEl.selectAll('*').remove();

		const root = svgEl
			.attr('width', width + margin.left + margin.right)
			.attr('height', height + margin.top + margin.bottom);

		const g = root.append('g').attr('transform', `translate(${margin.left},${margin.top})`);

		const x = d3
			.scaleLinear()
			.domain(d3.extent(cleanData, (d) => d.Year))
			.nice()
			.range([0, width]);

		const y = d3
			.scaleLinear()
			.domain([0, d3.max(cleanData, (d) => d.Global_Sales)])
			.nice()
			.range([height, 0]);

		const r = d3
			.scaleSqrt()
			.domain([d3.min(cleanData, (d) => d.Critic_Score), d3.max(cleanData, (d) => d.Critic_Score)])
			.range([4, 14]);

		const xAxis = g
			.append('g')
			.attr('transform', `translate(0,${height})`)
			.call(d3.axisBottom(x).tickFormat(d3.format('d')));

		xAxis.selectAll('text').attr('fill', '#e5e7eb').attr('font-size', '12px');
		xAxis.selectAll('.domain, .tick line').attr('stroke', '#6b7280');

		const yAxis = g.append('g').call(d3.axisLeft(y));

		yAxis.selectAll('text').attr('fill', '#e5e7eb').attr('font-size', '12px');
		yAxis.selectAll('.domain, .tick line').attr('stroke', '#6b7280');

		g.selectAll('circle.point')
			.data(cleanData)
			.enter()
			.append('circle')
			.attr('class', 'point')
			.attr('cx', (d) => x(d.Year))
			.attr('cy', (d) => y(d.Global_Sales))
			.attr('r', (d) => r(d.Critic_Score))
			.attr('fill', '#34d399')
			.attr('opacity', 0.75)
			.attr('stroke', '#0f172a')
			.attr('stroke-width', 1)
			.on('mousemove', (event, d) => {
				tooltip.style.opacity = '1';
				tooltip.style.left = `${event.pageX + 12}px`;
				tooltip.style.top = `${event.pageY - 28}px`;
				tooltip.innerHTML = `
					<strong>${d.Name}</strong><br>
					Jahr: ${d.Year}<br>
					Sales: ${d.Global_Sales} Mio.<br>
					Critic Score: ${d.Critic_Score}
				`;
			})
			.on('mouseleave', () => {
				tooltip.style.opacity = '0';
			});

		g.append('text')
			.attr('x', width / 2)
			.attr('y', height + 45)
			.attr('text-anchor', 'middle')
			.attr('fill', '#e5e7eb')
			.text('Release Year');

		g.append('text')
			.attr('transform', 'rotate(-90)')
			.attr('x', -height / 2)
			.attr('y', -45)
			.attr('text-anchor', 'middle')
			.attr('fill', '#e5e7eb')
			.text('Global Sales (Mio.)');
	}
</script>

<div class="chart-card">
	<h2>Scatterplot – Jahr vs. globale Verkäufe</h2>
	<svg bind:this={svg}></svg>
	<div class="tooltip" bind:this={tooltip}></div>
</div>

<style>
	.chart-card {
		background: #111827;
		padding: 1rem;
		border-radius: 18px;
		box-shadow: 0 10px 25px rgba(0, 0, 0, 0.25);
		position: relative;
		min-height: 430px;
	}

	h2 {
		margin: 0 0 1rem 0;
		color: white;
		font-size: 1.1rem;
	}

	svg {
		display: block;
		margin: 0 auto;
		max-width: 100%;
		height: auto;
	}

	.tooltip {
		position: fixed;
		background: rgba(0, 0, 0, 0.9);
		color: white;
		padding: 0.55rem 0.75rem;
		border-radius: 8px;
		font-size: 0.9rem;
		pointer-events: none;
		opacity: 0;
		transition: opacity 0.15s ease;
		z-index: 1000;
	}
</style>