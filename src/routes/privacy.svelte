<script type="text/javascript">
	import './../app.css';
	import Header from './../components/Header.svelte';
	import Footer from './../components/Footer.svelte';
</script>

<div class="wrapper">
	<Header showProductHunt={false}/>
	<div class="main">
		<p>We use Google Analytics to get the statistics of the site. We do not collect or store any other data.</p>
	</div>
	<div class="footer">
		<Footer />
	</div>
</div>

<style type="text/css">
	.wrapper{
		height: 100vh;
		background: #f0f0f0;
	}
	.main{
		padding: 2rem;
	}
	.footer{
		width: 100vw;
		position: absolute;
		bottom: 0;
	}
</style>