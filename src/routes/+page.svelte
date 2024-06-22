<style>
	.container {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		position: absolute;
		gap: 10px;
	}

	.modal {
		justify-content: center;
		align-items: center;
		flex-direction: column;
		width: 20rem;
		text-align:center;
		font-family: space grotesk;
		border-radius: 10px;
		backdrop-filter: blur(10px);
		box-shadow: 0 10px 50px rgb(0 0 0 / 0.5);
		padding: 20px;
	}

	.main {
		background-color: #ffffff21;
		border: 1px solid #ffffff21;
		color: #fff;
	}

	.warning {
		background-color: #ffc52721;
		border: 1px solid #ffc52721;
		color: #d4a625;
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

	.neutral {
		color: #fff;
		opacity: 0.5;
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

	function decryptString(input: string): string[] {
		const decryptionMap: { [key: string]: number } = {
			a: 9, b: 8, c: 7, d: 6, e: 5, f: 4, h: 2, i: 1
		};

		return input.split('|').map(chunk => {
			return chunk.trim().split(' ').map(subChunk => {
				const lowerCaseChar = subChunk.match(/[a-i]/)?.[0];
				let digit: number;

				if (lowerCaseChar === 'g') {
					digit = subChunk.includes('1') ? 0 : 3;
				} else if (lowerCaseChar) {
					digit = decryptionMap[lowerCaseChar];
				} else {
					throw new Error("Invalid input string format");
				}

				return digit;
			}).join('');
		});
	}

	async function handlePaste(event: ClipboardEvent) {
		const items = event.clipboardData?.items;
		if (items) {
			for (let i = 0; i < items.length; i++) {
				if (items[i].kind === 'file' && items[i].type.startsWith('image/')) {
					const file = items[i].getAsFile();
					if (file) {
						const formData = new FormData();
						formData.append('file', file);

						result = "Working, please wait...";
						state = "neutral";

						try {
							const response = await fetch("https://server.woffl.net/decode", {
								method: 'POST',
								body: formData
							});
							result = `Found codes: ${decryptString((await response.json()).content).join(', ')}`;
							state = "success";
							console.log(result);
						} catch (error) {
							result = "Something went wrong, input manually";
							state = "error";
							console.log(error);
						}
					}
				}
			}
		}
	}
</script>


<div class=container>
	<div class="modal main" on:paste={handlePaste}>
		<h1 >FFD Decoder</h1>
		<p style="margin-bottom:40px; margin-top:-20px">Input only the letters in the code <br><span style="font-size: 15px; opacity:0.5">Or paste an image of the code</span></p>
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

	<div class="modal warning">
		<h3>AI image features are experimental</h3>
		<p>Pasting the image a second time may fix issues, but it is always imperfect.</p>
	</div>
</div>