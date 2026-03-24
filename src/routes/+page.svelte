<script>
	import { onMount } from 'svelte';
	import * as d3 from 'd3';

	import PieChart from '$lib/components/PieChart.svelte';
	import ColumnChart from '$lib/components/ColumnChart.svelte';
	import ScatterPlot from '$lib/components/ScatterPlot.svelte';

	let data = [];
	let loading = true;

	onMount(async () => {
		const rawData = await d3.csv('/games.csv', d3.autoType);

		data = rawData.map((d) => ({
			...d,
			Year: +d.Year,
			Global_Sales: +d.Global_Sales,
			Critic_Score: +d.Critic_Score
		}));

		loading = false;
	});
</script>

<svelte:head>
	<title>svgCharts | Video Game Dashboard</title>
	<meta
		name="description"
		content="Interaktive Datenvisualisierung über Videospiele mit D3.js, SVG und Svelte." />
</svelte:head>

<div class="page">
	<section class="hero">
		<div class="hero-content">
			<h1>svgCharts</h1>
			<p>
				Interaktive Website zum Thema Videogames mit Pie Chart, Column Chart und Scatterplot.
			</p>
		</div>
	</section>

	<section class="intro">
		<h2>Über das Projekt</h2>
		<p>
			Diese Website zeigt Daten von bekannten Videospielen. Die Visualisierungen wurden mit
			D3.js und SVG erstellt und in eine Svelte-Website eingebaut. Ziel ist es, Genres,
			Plattformen und Verkaufszahlen übersichtlich und interaktiv darzustellen.
		</p>
	</section>

	{#if loading}
		<p class="loading">Daten werden geladen...</p>
	{:else}
		<section class="charts">
			<PieChart {data} />
			<ColumnChart {data} />
			<ScatterPlot {data} />
		</section>
	{/if}

	<section class="info">
		<h2>Was wird dargestellt?</h2>
		<div class="info-grid">
			<div class="info-card">
				<h3>Pie Chart</h3>
				<p>Zeigt die Verteilung der globalen Verkäufe nach Genre.</p>
			</div>

			<div class="info-card">
				<h3>Column Chart</h3>
				<p>Zeigt die Plattformen mit den höchsten globalen Verkaufszahlen.</p>
			</div>

			<div class="info-card">
				<h3>Scatterplot</h3>
				<p>Zeigt den Zusammenhang zwischen Erscheinungsjahr und globalen Sales.</p>
			</div>
		</div>
	</section>
</div>

<style>
	:global(body) {
		margin: 0;
		font-family: Arial, sans-serif;
		background: #0b1220;
		color: #e5e7eb;
	}

	:global(*) {
		box-sizing: border-box;
	}

	.page {
		min-height: 100vh;
	}

	.hero {
		min-height: 42vh;
		display: flex;
		align-items: center;
		justify-content: center;
		text-align: center;
		padding: 2rem 1rem;
		background:
			linear-gradient(rgba(2, 6, 23, 0.7), rgba(2, 6, 23, 0.82)),
			url('https://images.unsplash.com/photo-1511512578047-dfb367046420?auto=format&fit=crop&w=1600&q=80')
				center/cover no-repeat;
	}

	.hero-content {
		max-width: 850px;
	}

	h1 {
		margin: 0 0 1rem 0;
		font-size: 3rem;
		color: white;
	}

	.hero p {
		margin: 0 auto;
		max-width: 700px;
		font-size: 1.1rem;
		line-height: 1.7;
		color: #d1d5db;
	}

	.intro,
	.info {
		max-width: 1150px;
		margin: 0 auto;
		padding: 2rem 1rem;
	}

	.intro h2,
	.info h2 {
		margin-bottom: 0.8rem;
		color: white;
	}

	.intro p {
		line-height: 1.8;
		color: #d1d5db;
	}

	.loading {
		text-align: center;
		padding: 2rem;
		font-size: 1.1rem;
		color: #cbd5e1;
	}

	.charts {
		max-width: 1150px;
		margin: 0 auto;
		padding: 1rem;
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
		gap: 1.5rem;
		align-items: start;
	}

	.info-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
		gap: 1rem;
		margin-top: 1rem;
	}

	.info-card {
		background: #111827;
		padding: 1.2rem;
		border-radius: 16px;
		box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
	}

	.info-card h3 {
		margin-top: 0;
		margin-bottom: 0.6rem;
		color: white;
	}

	.info-card p {
		margin: 0;
		line-height: 1.7;
		color: #d1d5db;
	}

	@media (max-width: 700px) {
		h1 {
			font-size: 2.2rem;
		}

		.hero p {
			font-size: 1rem;
		}
	}
</style>