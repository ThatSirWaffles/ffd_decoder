<style>
	.main {
	justify-content: center;
	align-items: center;
	flex-direction: column;
	padding: 20px;
	border: 2px solid #000;
	width: 20rem;
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	text-align:center;
	font-family: space grotesk;
	border-radius: 10px;
	}

	.success {
		color: #38bf2e;
		opacity: 1;
		transition: 0.5s;
	}

	.error {
		color: #bf2e2e;
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

	function ord(char: string): number {
		return char.charCodeAt(0)-97;
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
		<input style="font-family: space grotesk; width: 4rem; font-size:15px; height: 30px; border-radius:20px 10px 10px 20px; padding-left: 10px; border: 2px solid" bind:value={input}>
		
		<button style="display: flex; justify-content: center; align-items: center; cursor: pointer; padding: 0px; background-color: transparent; font-family: space grotesk; width: 40px; font-size:15px ;height: 36px; border-radius:10px 20px 20px 10px; border: 2px solid" on:click={updateCode}>
			<span class="material-symbols-rounded">play_arrow</span>
		</button>
	</div>

	<p class={state}>{result}</p>
	<p style="margin-top: 50px"><i>For g, it can be either 0 or 3: the one with the lower numbers around it is 0, and the other one is 3</i></p>
	<p style="margin-top: 50px; margin-bottom:0px; opacity:0.5"><i>DM bugs/questions to @sirwaffles</i></p>
</div>