<svelte:options tag="exchange-rates" />
<script>
	import { onMount } from 'svelte';

	let rates = [];
	let error = false;
	let errorMessage = '';

	onMount(async () => {
		try {
			const resp = await fetch('https://api.exchangeratesapi.io/latest?base=THB')
				.then(res => res.json());
			const { rates: rawRates } = resp;
			rates = Object.keys(resp.rates)
				.sort()
				.map(currency => ({
					currency,
					rate: resp.rates[currency]
				}));
		} catch (e) {
			error = true;
			errorMessage = e.message;
		}
	})
</script>

<style>
	.container {
		width: 100%;
		border: 1px solid black;
		max-width: 320px;
	}
	* {
		font-family: 'Roboto', Tahoma, sans-serif;
	}
	p {
		margin: 0;
	}
	.left-padding {
		padding-left: 16px;
		padding-right: 16px;
	}
	.header {
		margin: 0;
		background-color: #1A237E;
		color: white;
		padding: 16px;
	}
	.title {
		font-weight: 700;
		font-size: 24px;
		margin-bottom: 8px;
	}
	.base-label {
		font-size: 14px;
	}
	.table-row {
		display: grid;
		grid-template-columns: 1fr 1fr;
	}
	.table-header {
		padding-top: 8px;
		padding-bottom: 8px;
		box-shadow: rgba(0, 0, 0, 0.04) 0px 7px 9px 0px;	
	}
	.table-content {
		max-height: 320px;
		overflow: scroll;
	}
	.table-content > .table-row {
		padding-top: 8px;
		padding-bottom: 8px;
	}
</style>

<div class="container">
	<div class="header left-padding">
		<p class="title">Exchange Rate</p>
		<p class="base-label">Base: THB</p>
	</div>
	{#if error}
		<p>Error: {errorMessage}</p>
	{:else}
		<div>
			<div class="table-row table-header left-padding">
				<p>Currency</p>
				<p>Rate</p>
			</div>
			<div class="table-content left-padding">
				{#each rates as rate}
					<div class="table-row">
						<p>{rate.currency}</p>
						<p>{(1 / rate.rate).toFixed(2)}</p>
					</div>
				{/each}
			</div>
		</div>
	{/if}
</div>