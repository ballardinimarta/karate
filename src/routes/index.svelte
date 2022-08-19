<script lang="ts">
	import { onMount } from 'svelte';
	let number = 0;
	function shuffle(array: any[]) {
		let currentIndex = array.length,
			randomIndex;

		// While there remain elements to shuffle.
		while (currentIndex != 0) {
			// Pick a remaining element.
			randomIndex = Math.floor(Math.random() * currentIndex);
			currentIndex--;

			// And swap it with the current element.
			[array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
		}

		return array;
	}
	let cards: any[] = [];

	const endpoint = `https://api.storyblok.com/v2/cdn/stories?cv=${Date.now()}&token=${
		import.meta.env.VITE_ACCESS_TOKEN
	}&version=published&starts_with=cards&per_page=100`;

	onMount(async function () {
		const response = await fetch(endpoint);
		const data = await response.json();
		cards = data.stories;
		cards = shuffle(cards);
		cards.push({
			content: {
				title: 'SLUT',
				description: 'Nu är spelet slut, refresha sidan om ni vill spela igen!',
				image: {
					filename: ''
				}
			}
		});
	});

	let flipped = false;
	function handleClick() {
		flipped = !flipped;
		console.log(cards);
	}
</script>

<h1>KarateFylla</h1>
<div class="card-container flip-box">
	<div class="card-bg flip-box-inner" class:flip-it={flipped}>
		{#if cards.length > 0}
			{#if cards[number].content.image.filename === ''}
				<div
					class="card flip-box-front no-image"
					src={cards[number].content.image.filename}
					alt="kort"
					on:click={handleClick}
				>
					<p>{cards[number].content.title}</p>
				</div>
			{:else}<img
					class="card flip-box-front"
					src={cards[number].content.image.filename}
					alt="kort"
					on:click={handleClick}
				/>{/if}

			<div class:conceal-answer={flipped} class="flip-box-back" on:click={handleClick}>
				<div class="card-description">
					<h3>{cards[number].content.title}</h3>
					<p>{cards[number].content.description}</p>
				</div>
			</div>
		{/if}
	</div>
</div>
<div class="button-container">
	<button
		class="next-button"
		on:click={() => {
			number = number + 1;
			flipped = false;
		}}>nästa kort</button
	>
</div>

<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" />
<link
	href="https://fonts.googleapis.com/css2?family=Roboto+Mono&family=Silkscreen&display=swap"
	rel="stylesheet"
/>

<style lang="css">
	@import url('https://use.typekit.net/bqr3kzy.css');

	:global(body) {
		height: 100vh;
		background: linear-gradient(45deg, rgba(121, 150, 233, 1) 0%, rgb(176, 160, 237) 100%);
	}
	.flip-box {
		background-color: transparent;
		-webkit-perspective: 1000px;
		perspective: 1000px; /* Remove this if you don't want the 3D effect */
	}
	.card.no-image {
		background-color: black;
		display: flex;
		justify-content: center;
		align-items: center;
		aspect-ratio: 57 / 89;
		width: 90%;
	}
	.card.no-image > p {
		font-family: gurkner-jump, sans-serif;
		font-weight: 400;
		font-style: normal;
		font-size: 15vw;
		text-align: center;
		padding: 0px 10px;
		color: white;
		text-shadow: 2px 3px rgb(96, 120, 186);
	}
	/* This container is needed to position the front and back side */
	.flip-box-inner {
		transition: transform 0.5s;
		-webkit-transition: transform 0.5s;
		-webkit-transition: -webkit-transform 0.5s;
		transform-style: preserve-3d;
		-webkit-transform-style: preserve-3d;
	}

	/* Do an horizontal flip on button click */
	.flip-it {
		-webkit-transform: rotateY(180deg);
		transform: rotateY(180deg);
	}
	h1 {
		text-align: center;
		margin-bottom: 10px;
		font-family: 'Silkscreen', cursive;
	}
	.card-container {
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.card-bg {
		display: flex;
		justify-content: center;
		align-items: center;
		aspect-ratio: 57 / 89;
		width: 90%;
		border-radius: 15px;
		background-color: white;
		box-shadow: -3px 3px 15px #00000048;
	}
	.card-description {
		text-align: center;
		font-family: 'Roboto Mono', monospace;
	}
	.card-description > h3 {
		font-family: 'Silkscreen', cursive;
	}
	.flip-box-front,
	.flip-box-back {
		position: absolute;
		-webkit-backface-visibility: hidden; /* Safari */
		backface-visibility: hidden;
	}

	/* Style the front side */
	.flip-box-front {
		background-color: #ddd;
		color: black;
		display: flex;
		justify-content: center;
	}

	@keyframes revealTextSlowly {
		to {
			color: rgb(44, 44, 44);
		}
	}

	.conceal-answer {
		animation: revealTextSlowly 0.3s forwards;
	}

	.flip-box-back {
		-webkit-transform: rotateY(180deg);
		transform: rotateY(180deg);
		height: 90%;
		width: 90%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		background-color: rgb(255, 187, 220);
	}
	.card-description {
		padding: 0px 10px;
	}
	.button-container {
		margin-top: 20px;
		display: flex;
		justify-content: center;
	}
	.next-button {
		all: unset;
		font-family: 'Silkscreen', cursive;
		border: 2px solid black;
		padding: 10px 20px;
		border-radius: 15px;
		background-color: rgb(250, 250, 250);
		box-shadow: 0px 3px 0px black;
	}
	.next-button:active {
		transform: translateY(3px);
		box-shadow: 0px 0px 0px black;
	}
</style>
