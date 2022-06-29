<script type="text/javascript">
	import './../app.css';
	import Header from './../components/Header.svelte';
	import Charts from './../components/charts/Charts.svelte';
	import Download from './../components/download/Download.svelte';
	import SubMain from './../components/SubMain.svelte';
	import Footer from './../components/Footer.svelte';
</script>

<div class="wrapper">
	<Header showProductHunt={false}/>
	<div class="main">
		<Charts />
	</div>
	<div class="download">
		<Download />
	</div>
	<SubMain />
	<Footer />
</div>

<style type="text/css">
	.main{
		width: 900px;
		height: 575px;
		margin: 3rem auto .5rem;
		display: flex;
		border: 1px solid #000;
		background: #fff;
	}
	.download{
		width: 900px;
		margin: 0 auto 4rem;
		display: flex;
		justify-content: flex-end;
	}
</style>