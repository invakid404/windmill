<script lang="ts">
	import { classNames } from '$lib/utils'
	import { createEventDispatcher, getContext } from 'svelte'

	import type { AppViewerContext, ContextPanelContext } from '../../types'
	import { connectInput } from '../appUtils'
	import PanelSection from '../settingsPanel/common/PanelSection.svelte'
	import ComponentOutput from './ComponentOutput.svelte'
	import ComponentOutputViewer from './ComponentOutputViewer.svelte'
	import BackgroundScriptsOutput from './components/BackgroundScriptsOutput.svelte'
	import OutputHeader from './components/OutputHeader.svelte'
	import { ClearableInput } from '../../../common'
	import DocLink from '../settingsPanel/DocLink.svelte'
	import HideButton from '../settingsPanel/HideButton.svelte'

	const { connectingInput, app } = getContext<AppViewerContext>('AppViewerContext')
	const { search } = getContext<ContextPanelContext>('ContextPanel')

	const dispatch = createEventDispatcher()

	let hasState: boolean = false
</script>

<PanelSection noPadding titlePadding="px-2 pt-2" title="Outputs">
	<svelte:fragment slot="action">
		<div class="p-0.5">
			<HideButton
				on:click={() => {
					dispatch('hidePanel')
				}}
				direction="left"
			/>
			<DocLink docLink="https://www.windmill.dev/docs/apps/outputs" />
		</div>
	</svelte:fragment>
	<div
		class={classNames(
			'bg-surface w-full h-full z-30 overflow-auto',
			$connectingInput.opened
				? 'border-blue-500 border-t-2 border-r-2 bg-blue-50/50 dark:bg-frost-900/50 z-50'
				: ''
		)}
	>
		<div class="min-w-[150px]">
			<div class="sticky z-10 top-0 left-0 w-full p-1.5 bg-surface">
				<ClearableInput bind:value={$search} placeholder="Search outputs..." />
			</div>

			<div class="flex flex-col gap-4">
				<div>
					<span class="text-xs font-semibold text-secondary p-2">State & Context</span>

					<OutputHeader selectable={false} id={'ctx'} name={'App Context'} first color="blue">
						<ComponentOutputViewer
							componentId={'ctx'}
							on:select={({ detail }) => {
								$connectingInput = connectInput($connectingInput, 'ctx', detail)
							}}
						/>
					</OutputHeader>

					<OutputHeader
						selectable={false}
						id={'state'}
						name={'State'}
						color="blue"
						disabled={!hasState}
					>
						<ComponentOutputViewer
							bind:hasContent={hasState}
							componentId={'state'}
							on:select={({ detail }) => {
								$connectingInput = connectInput($connectingInput, 'state', detail)
							}}
						/>
					</OutputHeader>
				</div>

				<div>
					<span class="text-xs font-semibold text-secondary p-2">Components</span>
					{#each $app.grid as gridItem, index (gridItem.id)}
						<ComponentOutput {gridItem} first={index === 0} />
					{/each}
				</div>
				<div>
					<span class="text-xs font-semibold text-secondary p-2">Background runnables</span>
					<BackgroundScriptsOutput />
				</div>
			</div>
		</div>
	</div>
</PanelSection>
