<script lang="ts">
	import Icon from '@iconify/svelte';
	import '../app.css';
	let { children } = $props();
</script>

<div class="absolute w-full bg-emerald-950 py-2">
	<h1 class="ml-2 text-center text-lime-500">•Password Please•</h1>
</div>
{@render children()}

<div class="absolute bottom-0 flex w-full justify-between bg-emerald-950 py-2 text-lime-500">
	<div class="ml-2 flex flex-row gap-2 transition-all duration-200">
		<a
			href="https://github.com/taariqelliott"
			class="transition-all duration-200 hover:opacity-70 active:scale-90 active:opacity-70"
			target="”_blank”"><Icon icon="iconoir:github" width="30" height="30" /></a
		>
		<a
			href="https://www.linkedin.com/in/taariq-elliott/"
			class="transition-all duration-200 hover:opacity-70 active:scale-90 active:opacity-70"
			target="”_blank”"
			><Icon icon="iconoir:linkedin" width="30" height="30" />
		</a>
		<a
			href="https://taariqelliott.netlify.app/"
			class="transition-all duration-200 hover:opacity-70 active:scale-90 active:opacity-70"
			target="”_blank”"
			><Icon icon="iconoir:code" width="30" height="30" />
		</a>
	</div>
	<div class="mr-2 flex items-center justify-center text-sm hover:underline">
		<a href="mailto:codebytaariq@gmail.com">codebytaariq@gmail.com</a>
	</div>
</div>
