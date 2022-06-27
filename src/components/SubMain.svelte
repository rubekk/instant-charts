<script type="text/javascript">
	const shareLink = "todopomo.netlify.app";
	const fbShareLink = `https://www.facebook.com/sharer/sharer.php?u=${shareLink}`; 
	const twitterShareLink = `http://twitter.com/share?&url=${shareLink}`;
	const redditShareLink = `https://www.reddit.com/submit?url=${shareLink}`;
	const linkedinShareLink = `https://www.linkedin.com/shareArticle?mini=true&url=${shareLink}`;

	const shareOnSocial = link=>{
		const navUrl = link;
		window.open(navUrl, '_blank');
	}
</script>

<div class="sub-main">
	<div class="explain-sec">
		<h2>Simple and instant charts</h2>
		<ul>
			<li>➡️ No sign ups</li>
			<li>➡️ Simple interface</li>
			<li>➡️ PDF and PNG download</li>
		</ul>
	</div>
	<div class="lower-sub-main">
		<div class="share-sec">
			<h3>Share this page</h3>
			<div class="share-icons-sec">
				<i class="fa-brands fa-facebook-square" on:click={()=>shareOnSocial(fbShareLink)}></i>
				<i class="fa-brands fa-linkedin" on:click={()=>shareOnSocial(linkedinShareLink)}></i>
				<i class="fa-brands fa-twitter-square" on:click={()=>shareOnSocial(twitterShareLink)}></i>
				<i class="fa-brands fa-reddit-square" on:click={()=>shareOnSocial(redditShareLink)}></i>
			</div>
		</div>
		<div class="coffee-sec">
			<h3>Buy me a coffee</h3>
		</div>
	</div>
</div>

<style type="text/css">
	.sub-main{
		padding: 5rem 0;
		background: #e8e8e8;
	}
	h2, h3{
		color: #797979;
	}
	.explain-sec{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.explain-sec ul{
		margin-top: 1rem;
	}
	.lower-sub-main{
		margin: 5rem 0 0;
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}
	.share-icons-sec i{
		margin: .5rem;
		font-size: 2rem;
		cursor: pointer;
	}
	.share-icons-sec i:first-child{
		margin-left: 0;
	}
</style>