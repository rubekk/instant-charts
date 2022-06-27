<script type="text/javascript">
	export let showProductHunt = false;
</script>

<div class="header">
	<div class="header-left">
		<h1>Instant-Charts</h1>
	</div>
	<div class="header-right" style={`display:${showProductHunt?'block':'none'}`}>
		<h2>Product Hunt</h2>
	</div>
</div>

<style>
	.header{
		padding: 1rem;
		display: flex;
		justify-content: space-between;
		align-items: center;
		background: #e8e8e8;
	}
	.header-left h1, .header-right h2{
		color: #797979;
		cursor: pointer;
	}
	.header-right{
		display: flex;
	}
</style>