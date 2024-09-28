<style>
	body {
    display: grid;
    place-items: center; 
    margin: 0;
	padding: 10px;
    }
	textarea {
    height: 400px;
    resize: vertical;
    border-radius: 5px;
    width: 300px;
  }

  p {
    text-align: match-parent; 
  }
  .loading-spinner {
        border: 8px solid #f3f3f3; 
        border-top: 8px solid #3498db; 
        border-radius: 50%;
        width: 40px;
        height: 40px;
        animation: spin 1s linear infinite;
    }

    @keyframes spin {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }

</style>

<script lang="ts">
	import axios from 'axios';
	let multiplier = 100;
	let responseData = '';
	let data = "";
	let loading = false;
	async function listData(){
		responseData = '';
		loading = true;
		// const dataitems = data.split(('\n'))
		const lines = data.split('\n');
		const dataitems = lines.filter(line => 
		/\(\d+\)/.test(line) && !line.startsWith('App:')
		).map(line => line.replace(/\s\(\d+\)/, ''));
	
		try {
			  const response = await axios({
			  method: 'post',
        // url: 'http://localhost:8080/calculate',
			  url: 'https://buyhammer-muddy-lake-2945.fly.dev/calculate',
			  data: {
				  dataitems,
				  multiplier
			  }
			  });
		  responseData = response.data.message;
		  loading = false;
		} catch(error){
		  loading = false;
	
		  console.error('Error while fetching data:', error);
	
		  if (axios.isAxiosError(error)) {
			  responseData = error.response?.data?.message || 'An error occurred while processing the request.';
		  } else {
			  responseData = 'An unexpected error occurred.';
		  }
	  }
	}
	</script>
	
	<body>
	<h1 class="h1" >Welcome to Buyhammer!</h1>
	<h3 class="h3" style="padding-top: 10px">Copy paste your list from the AoS modbile app text in the box below to calculate the MSRP value.</h3>
	<h4 class="h4" style="padding-top: 10px">Note: This app cannot currently calculate reinforced units, so please enter each unit individually.</h4>
	<div>
	  <textarea style="margin: 15px;" class="textarea" placeholder="Paste your list here..." bind:value={data}></textarea>
	</div>
	<h4 class="h4" style="padding-top: 10px">% of MSRP</h4>
	<label style="place-text: center" class="label">
		<input class="input" title="Input (readonly)" readonly="true" bind:value={multiplier} min="0" max="100" />
		<input type="range"  bind:value={multiplier} min="0" max="100" />
	</label>
	{#if loading}
		<div class="loading-spinner"></div>
	{/if}
	{#if responseData}
		<div>
			<p>{responseData}</p>
		</div>
	{/if}
	<div>
	  <button type="button" class="btn variant-filled" on:click={listData}>Submit</button>
	</div>
	</body>
