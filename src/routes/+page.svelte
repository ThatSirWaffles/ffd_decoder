<style>
	.main {
	justify-content: center;
	align-items: center;
	flex-direction: column;
	padding: 20px;
	width: 20rem;
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	text-align:center;
	font-family: space grotesk;
	border-radius: 10px;
	background-color: #ffffff21;
	backdrop-filter: blur(10px);
	color: #fff;
	box-shadow: 0 10px 50px rgb(0 0 0 / 0.5);
	border: 1px solid #ffffff21
	}

	.success {
		color: #34e028;
		opacity: 1;
		transition: 0.5s;
	}

	.error {
		color: #e42525;
		opacity: 1;
		transition: 0.5s;
	}

	.output {
		opacity: 0;
	}
</style>

<script lang="ts">
	let result = "";
	let input = "";
	let state = "output";

	function handleKeyDown(event: KeyboardEvent) {
		if (event.key === "Enter") {
			updateCode();
		}
	}

	function updateCode() {
		if (input.length === 4 || input.length === 6) {
			if (/^[a-i]+$/.test(input)) {
				let a = [];
				for (let i = 0; i < input.length; i++) {
					if (input[i] == "g") {
						a.push("(0/3)");
					} else {
						let charCode = input.charCodeAt(i)-97;
						a.push('i'.charCodeAt(0)-97 - charCode + 1);
					}
				}

				result = `The code is ${a.join('')}`;
				state = "success";
			} else {
				result = "Expected characters a to i";
				state = "error";
			}
		} else {
			result = "Expected 4/6 characters";
			state = "error";
		}
	}
</script>

<div class=main>
	<h1 >FFD Decoder</h1>
	<p style="margin-bottom:40px; margin-top:-20px">Input only the letters in the code</p>
	<div style="display: flex; align-items: center; justify-content: center; gap: 5px">
		<input style="color: #fff; background-color: transparent; font-family: space grotesk; width: 4rem; font-size:15px; height: 30px; border-radius:20px 10px 10px 20px; padding-left: 10px; border: 1px solid #fff" bind:value={input} on:keydown={handleKeyDown}>
		
		<button style="display: flex; justify-content: center; align-items: center; cursor: pointer; padding: 0px; background-color: transparent; font-family: space grotesk; width: 40px; font-size:15px ;height: 36px; border-radius:10px 20px 20px 10px; border: 1px solid #fff" on:click={updateCode}>
			<span style="color: #fff" class="material-symbols-rounded">play_arrow</span>
		</button>
	</div>

	<p class={state}>{result}</p>
	<p style="margin-top: 50px"><i>For g, it can be either 0 or 3: the one with the lower numbers around it is 0, and the other one is 3</i></p>
	<p style="margin-top: 50px; margin-bottom:0px; opacity:0.5"><i>DM bugs/questions to @sirwaffles</i></p>
</div>