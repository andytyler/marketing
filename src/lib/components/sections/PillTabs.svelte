<script lang="ts" module>
	import { onMount, onDestroy } from "svelte"

	import TwoSide from "$components/layouts/TwoSplit.svelte"
	import * as Tabs from "$lib/components/ui/tabs"
	import { cn } from "$lib/utils"

	export interface PillTabsProps {
		defaultValue?: string
		autoProgressInterval?: number
		className?: string
	}
</script>

<script lang="ts">
	let { defaultValue = "in-house-counsel", autoProgressInterval = 3000, className }: PillTabsProps = $props()

	const options = [
		{
			id: "in-house-counsel",
			label: "In-House Counsel",
		},
		{
			id: "innovation-teams",
			label: "Innovation Teams",
		},
		{
			id: "transactional-work",
			label: "Transactional Work",
		},
		{
			id: "litigation-work",
			label: "Litigation Work",
		},
	]

	let activeValue = $state(defaultValue)
	let isAutoProgressing = $state(true)
	let intervalId: number | null = null
	let resetTimeoutId: number | null = null

	function startAutoProgression() {
		if (!isAutoProgressing || options.length <= 1) return

		intervalId = setInterval(() => {
			if (isAutoProgressing) {
				const currentIndex = options.findIndex((opt) => opt.id === activeValue)
				const nextIndex = (currentIndex + 1) % options.length
				activeValue = options[nextIndex].id
			}
		}, autoProgressInterval)
	}

	function stopAutoProgression() {
		if (intervalId) {
			clearInterval(intervalId)
			intervalId = null
		}
		isAutoProgressing = false
	}

	function restartAutoProgression() {
		isAutoProgressing = true
		startAutoProgression()
	}

	function handleTabChange(value: string) {
		stopAutoProgression()
		activeValue = value
	}

	function handleTabHover(value: string) {
		stopAutoProgression()
		activeValue = value

		// Clear any existing reset timeout
		if (resetTimeoutId) {
			clearTimeout(resetTimeoutId)
		}

		// Set a new timeout to restart auto-progression after 10 seconds
		resetTimeoutId = setTimeout(() => {
			restartAutoProgression()
		}, 10000)
	}

	function handleContentHover() {
		stopAutoProgression()

		// Clear any existing reset timeout
		if (resetTimeoutId) {
			clearTimeout(resetTimeoutId)
		}

		// Set a new timeout to restart auto-progression after 10 seconds
		resetTimeoutId = setTimeout(() => {
			restartAutoProgression()
		}, 10000)
	}

	function handleContentLeave() {
		// Clear any existing reset timeout
		if (resetTimeoutId) {
			clearTimeout(resetTimeoutId)
		}

		// Restart auto-progression immediately
		restartAutoProgression()
	}

	onMount(() => {
		startAutoProgression()
	})

	onDestroy(() => {
		if (intervalId) {
			clearInterval(intervalId)
		}
		if (resetTimeoutId) {
			clearTimeout(resetTimeoutId)
		}
	})
</script>

{#snippet inHouseCounselContent()}
	<TwoSide
		label="In-House Counsel"
		heading="Transform legal operations with AI-powered insights"
		description="Streamline contract review, compliance monitoring, and legal research with intelligent automation designed for in-house legal teams."
		buttonText="Learn More"
		buttonHref="/in-house-counsel">
		<div class="flex items-center justify-center">
			<img src="/images/features/concept.gif" alt="In-House Counsel" class="h-auto max-w-full" />
		</div>
	</TwoSide>
{/snippet}

{#snippet innovationTeamsContent()}
	<TwoSide
		label="Innovation Teams"
		heading="Accelerate innovation with AI-driven research"
		description="Leverage cutting-edge AI to analyze market trends, consumer behavior, and competitive landscapes for breakthrough innovations."
		buttonText="Explore Solutions"
		buttonHref="/innovation-teams">
		<div class="flex items-center justify-center">
			<img src="/images/features/image.png" alt="Innovation Teams" class="h-auto max-w-full" />
		</div>
	</TwoSide>
{/snippet}

{#snippet transactionalWorkContent()}
	<TwoSide
		label="Transactional Work"
		heading="Streamline due diligence and deal analysis"
		description="Make due diligence, contract review, and analysis faster, more accurate, and less manual with AI-powered insights."
		buttonText="See Solutions"
		buttonHref="/transactional-work">
		<div class="flex items-center justify-center">
			<img src="/images/features/steal.png" alt="Transactional Work" class="h-auto max-w-full" />
		</div>
	</TwoSide>
{/snippet}

{#snippet litigationWorkContent()}
	<TwoSide
		label="Litigation Work"
		heading="Enhance litigation strategy with AI insights"
		description="Analyze case law, predict outcomes, and develop winning strategies with AI-powered legal research and analysis tools."
		buttonText="Discover More"
		buttonHref="/litigation-work">
		<div class="flex items-center justify-center">
			<img src="/images/features/test.png" alt="Litigation Work" class="h-auto max-w-full" />
		</div>
	</TwoSide>
{/snippet}

<div class={cn("space-y-6", className)}>
	<Tabs.Root value={activeValue} onValueChange={handleTabChange}>
		<!-- Custom Pills List -->
		<div class="flex flex-wrap w-2/3 gap-8 mx-auto">
			{#each options as option}
				<div role="button" onmouseenter={() => handleTabHover(option.id)} tabindex="-1">
					<Tabs.Trigger
						value={option.id}
						class="data-[state=active]:bg-card  data-[state=active]:text-card-foreground data-[state=active]:border-border data-[state=inactive]:text-foreground data-[state=inactive]:border-border hover:bg-muted focus-visible:ring-ring/50 relative overflow-hidden rounded-full border px-4 py-2 text-sm font-medium transition-colors focus-visible:ring-[3px] focus-visible:outline-none disabled:opacity-50 data-[state=inactive]:bg-transparent">
						{#if option.id === activeValue && autoProgressInterval}
							<div class="progress-bar opacity-40 inset-0 pointer-events-none" aria-hidden="true"></div>
						{/if}
						<span class="relative z-10">{option.label}</span>
					</Tabs.Trigger>
				</div>
			{/each}
		</div>

		<!-- Tab Content -->
		<Tabs.Content value="in-house-counsel" class="mt-6">
			<div
				role="tabpanel"
				tabindex="-1"
				onmouseenter={() => handleContentHover()}
				onmouseleave={handleContentLeave}>
				{@render inHouseCounselContent()}
			</div>
		</Tabs.Content>

		<Tabs.Content value="innovation-teams" class="mt-6">
			<div
				role="tabpanel"
				tabindex="-1"
				onmouseenter={() => handleContentHover()}
				onmouseleave={handleContentLeave}>
				{@render innovationTeamsContent()}
			</div>
		</Tabs.Content>

		<Tabs.Content value="transactional-work" class="mt-6">
			<div
				role="tabpanel"
				tabindex="-1"
				onmouseenter={() => handleContentHover()}
				onmouseleave={handleContentLeave}>
				{@render transactionalWorkContent()}
			</div>
		</Tabs.Content>

		<Tabs.Content value="litigation-work" class="mt-6">
			<div
				role="tabpanel"
				tabindex="-1"
				onmouseenter={() => handleContentHover()}
				onmouseleave={handleContentLeave}>
				{@render litigationWorkContent()}
			</div>
		</Tabs.Content>
	</Tabs.Root>
</div>

<style>
	.progress-bar {
		animation: progress 4s linear 0s alternate;
		background: linear-gradient(to right, #000000, #000000);
		position: absolute;
		z-index: 0;
		height: 100%;
	}

	@keyframes progress {
		0% {
			width: 0%;
		}
		100% {
			width: 100%;
		}
	}
</style>
