<svelte:options runes={false} />

<script>
	import PieChart from '$lib/components/PieChart.svelte';
	import ColumnChart from '$lib/components/ColumnChart.svelte';
	import ScatterPlot from '$lib/components/ScatterPlot.svelte';
	import { games } from '$lib/data/games.js';

	const metrics = [
		{ value: '18', label: 'Games in dataset' },
		{ value: '3', label: 'Chart types' },
		{ value: 'SVG', label: 'Scalable visual output' }
	];
</script>

<svelte:head>
	<title>SVG CHARTS</title>
	<meta
		name="description"
		content="Professional SVG-based data visualization project with Pie Chart, Column Chart and Scatterplot."
	/>
</svelte:head>

<div class="page">
	<section class="hero">
		<div class="container hero-grid">
			<div class="hero-copy">
				<div class="eyebrow">Data Visualization Project</div>
				<h1>SVG CHARTS</h1>
				<p class="hero-text">
					A structured visualization project that presents one videogame dataset through
					three distinct analytical views: distribution, comparison and correlation.
				</p>
			</div>

			<div class="hero-panel">
				<div class="panel-label">Overview</div>

				<div class="metric-grid">
					{#each metrics as item}
						<div class="metric-card">
							<div class="metric-value">{item.value}</div>
							<div class="metric-label">{item.label}</div>
						</div>
					{/each}
				</div>
			</div>
		</div>
	</section>

	<section class="section container">
		<div class="section-top">
			<div>
				<div class="section-label">Project Scope</div>
				<h2>One dataset. Three analytical views.</h2>
			</div>

			<p class="section-intro">
				The website uses a fixed dataset and transforms it into three SVG-based charts.
				Each chart focuses on a different question and reveals a different layer of the same data.
			</p>
		</div>

		<div class="insight-grid">
			<article class="insight-card">
				<div class="card-tag">Distribution</div>
				<h3>Pie Chart</h3>
				<p>Shows how global sales are distributed across game genres.</p>
			</article>

			<article class="insight-card">
				<div class="card-tag">Comparison</div>
				<h3>Column Chart</h3>
				<p>Compares total global sales across the available platforms.</p>
			</article>

			<article class="insight-card">
				<div class="card-tag">Correlation</div>
				<h3>Scatterplot</h3>
				<p>Displays the relationship between release year and global sales.</p>
			</article>
		</div>
	</section>

	<section class="section container">
		<div class="section-top charts-head">
			<div>
				<div class="section-label">Visual Output</div>
				<h2>Interactive SVG Charts</h2>
			</div>

			<p class="section-intro">
				All charts are rendered from the same source data and structured for clarity, readability
				and interaction.
			</p>
		</div>

		<div class="chart-stack">
			<div class="chart-shell">
				<PieChart data={games} />
			</div>

			<div class="chart-shell">
				<ColumnChart data={games} />
			</div>

			<div class="chart-shell">
				<ScatterPlot data={games} />
			</div>
		</div>
	</section>

	<footer class="footer container">
		<div class="footer-line"></div>
		<p>SVG Charts — Data Visualization with structured interactive views.</p>
	</footer>
</div>