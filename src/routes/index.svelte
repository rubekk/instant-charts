<script type="text/javascript">
	import './../app.css';
	import Header from './../components/Header.svelte';
	import Charts from './../components/charts/Charts.svelte';
	import SubMain from './../components/SubMain.svelte';
	import Footer from './../components/Footer.svelte';
</script>

<div class="wrapper">
	<Header showProductHunt={true}/>
	<div class="main">
		<Charts />
	</div>
	<SubMain />
	<Footer />
</div>

<style type="text/css">
	.main{
		width: 900px;
		height: 575px;
		margin: 3rem auto 5rem;
		display: flex;
		border: 1px solid #000;
		background: #fff;
	}
</style>