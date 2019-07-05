<script>
	import { onMount } from 'svelte';

	let fid = '';
	let pics = [];
	let peopleCount = 0;
	let interval;
	let isSpining;
	let index = 0;
	let pictures = [];
	let backgroundMusic;
	let clapMusic;

	function init() {
		
	}

	onMount(async () => {
		backgroundMusic = document.querySelector('.audio-background');
		clapMusic = document.querySelector('.audio-clap');
	});

	async function getImages() {
		backgroundMusic.play();
		const listId = fid.split(',');

		if (listId == false) return;
        
      const listPromise = listId.map(i => fetch(`https://graph.facebook.com/${i.trim()}/picture?type=large`));
      const result = await Promise.all(listPromise);

		peopleCount = 0;
      pics = result.filter(r => r.ok).map(r => {
		  peopleCount++;
		  return {visible: true, url: r.url};
	  });
		reset();
	  
    //   localStorage.setItem('sdf')
	  }
	  
	  function reset() {
		pictures.forEach(p => {
			p.style = {};
		});
	  }


    function random() {
		pictures = document.querySelectorAll('.pro5');
		if (pictures.length === 0) return;
		backgroundMusic.volumn = 1;
		isSpining = true;

		const wrapper = document.querySelector('.list-pro5');
		const y = wrapper.offsetHeight / 2;
		const x = wrapper.offsetWidth / 2;

		pictures.forEach(e => {
			e.style.transform = `translate(${x - e.offsetLeft}px,${y - e.offsetTop}px)`;
		});

	  setTimeout(() => {
		  interval = setInterval(nextPic, 100);

	  }, 300);

	}

	function nextPic() {

		if (index == pictures.length) index = 0;
		const style = pictures[index].style;
		const previousPic = pictures[index-1];
		const lastPic = pictures[pictures.length - 1];

		if (previousPic) {
			previousPic.style.opacity = 0;
			previousPic.style.transform = previousPic.style.transform.replace(`scale(1.2)`, '');
		} else {
			lastPic.style.opacity = 0;
			lastPic.style.transform = lastPic.style.transform.replace(`scale(1.2)`, '');
		}

		// current pic
		style.transform += `scale(1.2)`;
		style.opacity = 1;

		index++;
	}

	function stop() {
		if (!interval) return;
		clearInterval(interval);

		let timeout = 0;
		const randomCounter = Math.floor(Math.random() * 20) + 5;

		for (let i = 0; i <= randomCounter; i++) {
			timeout += 200;
			setTimeout(nextPic, timeout);
			if (i === randomCounter) setTimeout(() => {
				 clapMusic.play();
				  
				  pictures.forEach(p => {
					  if (p.style.opacity) p.style.transform += 'scale(2)';
				  });
				  isSpining = false;
			}, timeout);
		}

		backgroundMusic.volumn = 0.5;
	}

</script>

<style>
.list-pro5 {
	display: grid;
	grid-template-columns: repeat(auto-fill, 200px);
	/* grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); */
	/* grid-auto-rows: 200px minmax(200px, auto);
    grid-template-columns: repeat(5, 1fr); */
}

.pro5 {
	transition: transform ease-out 500ms, opacity 100ms;
	/* animation: fly-around 5s infinite; */
}

/* @keyframes fly-around {
  0% { transform: translateX(-50px) }
  25% { transform: translateY(-50px) translateX(50px) }
  50% { transform: translateY(50px) translateX(50px) }
  50% { transform: translateY(50px) translateX(-50px) }
  100% { transform: translateY(-50px) translateX(-50px) }
} */
</style>

Facebook id : <input bind:value={fid}>
<button on:click={getImages} disabled={isSpining}>get images</button>
<button on:click={random} disabled={isSpining}>spin</button>
<button on:click={stop} disabled={!isSpining}>stop</button>
<button on:click={reset} disabled={isSpining}>reset</button>
<span>total: {peopleCount}</span>

<audio class="audio-background" autoplay loop>
	<source src="xosomienbac.mp3" type="audio/mpeg"/>
</audio>
<audio class="audio-clap">
	<source src="clap.mp3" type="audio/mpeg"/>
</audio>

	<div class="list-pro5">
{#each pics as pic}

<img class="pro5"
     src={pic.url}
     alt="profile picture" />
	 <!-- {#if pic.visible}
{/if} -->
	
	<!-- <div class="pro5"
     style="width: 50px; height: 50px; background: yellow;"
     alt="profile picture" /> -->
{/each}
	</div>


