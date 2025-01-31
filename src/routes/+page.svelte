<script lang="ts">
	import { onMount } from 'svelte';
	import { letters, numbers, commonSymbols, specialSymbols } from './components/characters';
	import { copy } from 'svelte-copy';
	import { Stretch } from 'svelte-loading-spinners';

	let selectedLength: number | null = $state(8);
	let passwords: string[] = $state([]);
	let includeLetters = $state(true);
	let includeNumbers = $state(true);
	let includeCommonSymbols = $state(true);
	let includeSpecialSymbols = $state(false);
	let isLowerCase = $state(false);
	let isUpperCase = $state(false);
	let noSpaces = $state(false);
	let popupVisible = $state(false);
	let mountedPassword = $state('');
	let password = $state(' ');
	let inputElement: HTMLInputElement | null = $state(null);
	let pageLoader = $state(true);

	function generatePassword() {
		passwords = [];

		if (
			selectedLength === null ||
			(!includeLetters && !includeNumbers && !includeCommonSymbols && !includeSpecialSymbols)
		) {
			for (let idx = 0; idx < 5; idx++) {
				passwords.push(' ');
			}
			return;
		}

		let availableChars: (string | number)[] = [];
		if (includeLetters) availableChars.push(...letters);
		if (includeNumbers) availableChars.push(...numbers);
		if (includeCommonSymbols) availableChars.push(...commonSymbols);
		if (includeSpecialSymbols) availableChars.push(...specialSymbols);
		if (!includeCommonSymbols) noSpaces = false;

		for (let i = 0; i < 5; i++) {
			password = Array.from(
				{ length: selectedLength },
				() => availableChars[Math.floor(Math.random() * availableChars.length)]
			).join('');
			passwords.push(password);
		}
	}

	onMount(() => {
		let availableChars: (string | number)[] = [];
		if (includeLetters) availableChars.push(...letters);
		if (includeNumbers) availableChars.push(...numbers);
		if (includeCommonSymbols) availableChars.push(...commonSymbols);
		if (includeSpecialSymbols) availableChars.push(...specialSymbols);

		for (let i = 0; i < 5; i++) {
			password = '';
			mountedPassword = '';
			for (let j = 0; j < 8; j++) {
				mountedPassword += availableChars[Math.floor(Math.random() * availableChars.length)];
			}
			password += mountedPassword;
			passwords.push(password);
		}
	});

	function copyToClipboard() {
		popupVisible = true;
		setTimeout(() => {
			popupVisible = false;
		}, 2000);
	}

	function clearAllFields() {
		includeLetters = false;
		includeNumbers = false;
		includeCommonSymbols = false;
		includeSpecialSymbols = false;
		isLowerCase = false;
		isUpperCase = false;
		noSpaces = false;
		passwords = [];
		for (let idx = 0; idx < 5; idx++) {
			passwords.push('');
		}
	}

	setTimeout(() => {
		pageLoader = false;
	}, 1000);
</script>

{#if pageLoader}
	<div class="flex h-dvh flex-col items-center justify-center bg-emerald-900 text-zinc-100">
		<div class="scale-y-75">
			<Stretch size="100" color="#7bce00" unit="px" duration="1s" />
		</div>
	</div>
{:else}
	<div class="flex h-dvh flex-col items-center justify-center bg-emerald-900 text-zinc-100">
		<div class="flex flex-col space-y-2 pb-1">
			<div>
				<input
					type="checkbox"
					name="letters"
					bind:checked={includeLetters}
					onchange={() => generatePassword()}
					aria-label="Include letters"
				/>
				<label for="letters">Letters</label>
			</div>
			<div>
				<input
					type="checkbox"
					name="numbers"
					bind:checked={includeNumbers}
					onchange={() => generatePassword()}
					aria-label="Include numbers"
				/>
				<label for="numbers">Numbers</label>
			</div>
			<div>
				<input
					type="checkbox"
					name="commonSymbols"
					bind:checked={includeCommonSymbols}
					onchange={() => generatePassword()}
					aria-label="Include common symbols"
				/>
				<label for="commonSymbols">Common Symbols</label>
			</div>
			<div>
				<input
					type="checkbox"
					name="specialSymbols"
					bind:checked={includeSpecialSymbols}
					onchange={() => generatePassword()}
					aria-label="Include special symbols"
				/>
				<label for="specialSymbols">Special Symbols</label>
			</div>
			<div>
				<input
					type="checkbox"
					name="lowercase"
					disabled={!includeLetters}
					bind:checked={isLowerCase}
					onchange={() => {
						if (isUpperCase) {
							isUpperCase = false;
						}
					}}
					aria-label="Convert to lowercase"
				/>
				<label for="lowercase">Lowercase</label>
			</div>
			<div>
				<input
					type="checkbox"
					name="uppercase"
					disabled={!includeLetters}
					bind:checked={isUpperCase}
					onchange={() => {
						if (isLowerCase) {
							isLowerCase = false;
						}
					}}
					aria-label="Convert to uppercase"
				/>
				<label for="uppercase">Uppercase</label>
			</div>
			<div>
				<input
					type="checkbox"
					name="noSpaces"
					disabled={!includeCommonSymbols}
					bind:checked={noSpaces}
					aria-label="Remove spaces"
				/>
				<label for="noSpaces">No Spaces</label>
			</div>
		</div>

		<select
			name="length"
			class="w-48 rounded border bg-lime-500 py-1 text-emerald-950"
			bind:value={selectedLength}
			onchange={() => generatePassword()}
			aria-label="Select password length"
		>
			<option value={8} disabled selected>Select length...</option>
			{#each Array.from({ length: 57 }, (_, i) => i + 8) as length}
				<option value={length}>{length}</option>
			{/each}
		</select>
		<div class="flex flex-row gap-4">
			<button onclick={clearAllFields} aria-label="Clear all fields">
				<div
					class="mt-2 w-32 cursor-pointer rounded-lg border-2 border-lime-500 bg-zinc-800 px-1 py-3 text-sm text-zinc-100 transition-all duration-100 hover:opacity-90 active:scale-95 active:opacity-90"
				>
					Clear
				</div>
			</button>
			<button
				onclick={() => {
					let count = 0;
					let intervalId = setInterval(() => {
						generatePassword();
						count++;
						if (count >= 50) {
							clearInterval(intervalId);
						}
					}, 1);
				}}
				aria-label="Shuffle passwords"
			>
				<div
					class="mt-2 w-32 cursor-pointer rounded-lg border-2 border-lime-500 bg-zinc-800 px-1 py-3 text-sm text-zinc-100 transition-all duration-100 hover:opacity-90 active:scale-95 active:opacity-90"
				>
					Shuffle 🔀
				</div>
			</button>
		</div>

		<div class="relative mt-2 w-full max-w-sm">
			<input
				placeholder="Paste..."
				type="text"
				class="w-full flex-row justify-between gap-2 overflow-hidden rounded-lg bg-zinc-800 px-4 py-4 pr-16 text-zinc-100"
				bind:this={inputElement}
				aria-label="Paste input"
			/>
			<button
				type="button"
				class="transtion-all absolute top-1/2 right-4 mr-2 -translate-y-1/2 transform cursor-pointer rounded-md bg-lime-500 px-2 text-lg text-emerald-950 duration-100 hover:opacity-90 active:scale-90 active:opacity-90"
				onclick={() => {
					if (inputElement) {
						inputElement.value = '';
					}
				}}
				aria-label="Clear input"
			>
				&times;
			</button>
		</div>
		<div
			class="mt-2 flex h-[1px] w-80 max-w-sm flex-row justify-between gap-2 rounded-lg bg-lime-500 px-4 text-zinc-100"
		></div>
		{#each passwords as pw, index}
			{@const transformedPw = (() => {
				let result = pw;
				if (isLowerCase) {
					result = result.toLowerCase();
				} else if (isUpperCase) {
					result = result.toUpperCase();
				}
				if (noSpaces) {
					result = result.replace(/\s/g, '');
				}
				return result;
			})()}
			<div
				class="mt-2 flex w-full max-w-sm flex-row justify-between gap-2 rounded-lg bg-zinc-800 px-4 py-4 text-zinc-100"
			>
				<div
					id="password-{index}"
					class="hide-scrollbar flex-1 overflow-x-scroll whitespace-nowrap"
					aria-label={`Generated password ${index + 1}`}
				>
					{transformedPw}
				</div>
				<button
					disabled={pw.replace(/\s/g, '').length === 0}
					class="w-10 cursor-pointer text-center transition-all duration-100 hover:opacity-90 active:scale-90 disabled:cursor-not-allowed disabled:opacity-50"
					use:copy={{ text: transformedPw }}
					onclick={() => {
						copyToClipboard();
					}}
					aria-label={`Copy password ${index + 1}`}
				>
					<span id="copy-{index}">📋</span>
				</button>
			</div>
		{/each}
		{#if popupVisible}
			<div
				class="absolute top-12 right-2 max-w-[200px] overflow-hidden rounded-lg border-2 border-emerald-950 bg-lime-500 px-2 py-3 text-xs font-bold text-ellipsis whitespace-nowrap text-emerald-950 sm:top-auto sm:bottom-14"
				aria-live="polite"
			>
				Copied!
			</div>
		{/if}
	</div>
{/if}

<style>
	input[type='checkbox'] {
		accent-color: #7bce00;
	}
	.hide-scrollbar::-webkit-scrollbar {
		display: none;
	}
	.hide-scrollbar {
		-ms-overflow-style: none;
		scrollbar-width: none;
	}
</style>
