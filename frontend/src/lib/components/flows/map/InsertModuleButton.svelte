<script lang="ts">
	import { Menu } from '$lib/components/common'
	import { createEventDispatcher, getContext } from 'svelte'
	import { CheckCircle2, Code, Cross, GitBranch, Repeat, Square, Zap } from 'lucide-svelte'
	import StepGen from '$lib/components/copilot/StepGen.svelte'
	import type { FlowModule } from '$lib/gen'
	import BarsStaggered from '$lib/components/icons/BarsStaggered.svelte'
	import type { FlowBuilderWhitelabelCustomUi } from '$lib/components/custom_ui'
	import { twMerge } from 'tailwind-merge'

	const dispatch = createEventDispatcher()
	export let trigger = false
	export let stop = false
	export let open: boolean | undefined = undefined
	export let index: number
	export let funcDesc = ''
	export let modules: FlowModule[]
	export let disableAi = false

	$: !open && (funcDesc = '')
	let customUi: undefined | FlowBuilderWhitelabelCustomUi = getContext('customUi')
</script>

<Menu
	transitionDuration={0}
	pointerDown
	bind:show={open}
	noMinW
	placement="bottom-center"
	let:close
>
	<svelte:fragment slot="trigger">
		<button
			title="Add step"
			id={`flow-editor-add-step-${index}`}
			type="button"
			class={twMerge(
				'w-5 h-5 flex items-center justify-center',
				'outline-[1px] outline dark:outline-gray-500 outline-gray-300',
				'text-secondary',
				'bg-surface focus:outline-none hover:bg-surface-hover   rounded '
			)}
		>
			<Cross size={12} />
		</button>
	</svelte:fragment>
	<div id="flow-editor-insert-module">
		<StepGen on:insert {index} bind:funcDesc bind:open {close} {modules} {disableAi} />

		{#if funcDesc.length === 0}
			<div class="font-mono divide-y text-xs w-full text-secondary">
				<button
					class="w-full text-left py-2 px-3 hover:bg-surface-hover whitespace-nowrap flex flex-row gap-2 items-center"
					on:pointerdown={() => {
						close()
						dispatch('new', 'script')
					}}
					role="menuitem"
					tabindex="-1"
				>
					<Code size={14} />
					Action
				</button>
				{#if customUi?.triggers != false && trigger}
					<button
						class="w-full text-left py-2 px-3 hover:bg-surface-hover whitespace-nowrap flex flex-row gap-2 items-center"
						on:pointerdown={() => {
							close()
							dispatch('new', 'trigger')
						}}
						role="menuitem"
						tabindex="-1"
					>
						<Zap size={14} />
						Trigger
					</button>
				{/if}
				<button
					class="w-full text-left py-2 px-3 hover:bg-surface-hover whitespace-nowrap flex flex-row gap-2 items-center"
					on:pointerdown={() => {
						close()
						dispatch('new', 'approval')
					}}
					role="menuitem"
					tabindex="-1"
				>
					<CheckCircle2 size={14} />
					Approval/Prompt
				</button>
				<button
					class="w-full text-left py-2 px-3 hover:bg-surface-hover whitespace-nowrap flex flex-row gap-2 items-center"
					on:pointerdown={() => {
						close()
						dispatch('new', 'forloop')
					}}
					role="menuitem"
				>
					<Repeat size={14} />

					For Loop
				</button>
				<button
					class="w-full text-left py-2 px-3 hover:bg-surface-hover whitespace-nowrap flex flex-row gap-2 items-center"
					on:pointerdown={() => {
						close()
						dispatch('new', 'whileloop')
					}}
					role="menuitem"
				>
					<Repeat size={14} />

					While Loop
				</button>

				<button
					class="w-full text-left py-2 px-3 hover:bg-surface-hover whitespace-nowrap flex flex-row gap-2 items-center"
					on:pointerdown={() => {
						close()
						dispatch('new', 'branchone')
					}}
					role="menuitem"
				>
					<GitBranch size={14} />
					Branch to one
				</button>

				<button
					class="w-full text-left py-2 px-3 hover:bg-surface-hover whitespace-nowrap flex flex-row gap-2 items-center"
					on:pointerdown={() => {
						close()
						dispatch('new', 'branchall')
					}}
					role="menuitem"
				>
					<GitBranch size={14} />

					Branch to all
				</button>

				{#if customUi?.flowNode != false}
					<button
						class="w-full text-left py-2 px-3 hover:bg-surface-hover rounded-none whitespace-nowrap flex flex-row gap-2 items-center"
						on:pointerdown={() => {
							close()
							dispatch('new', 'flow')
						}}
						role="menuitem"
					>
						<BarsStaggered size={14} />
						Flow
					</button>
				{/if}
				{#if stop}
					<button
						class="w-full text-left py-2 px-3 hover:bg-surface-hover inline-flex gap-2.5"
						on:pointerdown={() => {
							close()
							dispatch('new', 'end')
						}}
						role="menuitem"
					>
						<Square size={14} />
						End Flow
					</button>
				{/if}
			</div>
		{/if}
	</div>
</Menu>
