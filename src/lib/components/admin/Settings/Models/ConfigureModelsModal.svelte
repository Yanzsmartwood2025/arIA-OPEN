<script lang="ts">
	import { getContext, onMount } from 'svelte';
	import { toast } from 'svelte-sonner';

	import Modal from '$lib/components/common/Modal.svelte';
	import Switch from '$lib/components/common/Switch.svelte';
	import { getModelsConfig, setModelsConfig } from '$lib/apis/configs';

	const i18n = getContext('i18n');

	export let show = false;
	export let initHandler = () => {};

	let loading = false;

	let metadata = {
		vision: true,
		web_search: false,
		file_upload: true,
		code_interpreter: false,
		builtin_tools: false
	};

	let params = {
		temperature: 0.8,
		top_p: 0.9,
		max_tokens: 2048,
		function_calling: 'native'
	};

	const loadConfig = async () => {
		loading = true;
		const token = localStorage.token;
		const res = await getModelsConfig(token).catch((error) => {
			toast.error(`${error}`);
			return null;
		});

		if (res) {
			metadata = { ...metadata, ...(res.DEFAULT_MODEL_METADATA ?? {}) };
			params = { ...params, ...(res.DEFAULT_MODEL_PARAMS ?? {}) };
		}
		loading = false;
	};

	const submitHandler = async () => {
		loading = true;
		const token = localStorage.token;

		const res = await setModelsConfig(token, {
			DEFAULT_MODEL_METADATA: metadata,
			DEFAULT_MODEL_PARAMS: params
		}).catch((error) => {
			toast.error(`${error}`);
			return null;
		});

		if (res) {
			toast.success($i18n.t('Models configuration saved'));
			initHandler();
			show = false;
		}
		loading = false;
	};

	$: if (show) {
		loadConfig();
	}
</script>

<Modal size="md" bind:show>
	<div class="text-gray-700 dark:text-gray-100">
		<div class="flex justify-between dark:text-gray-100 px-5 pt-4 pb-2">
			<div class="text-lg font-medium self-center">
				{$i18n.t('Configure Models')}
			</div>
			<button
				class="self-center"
				on:click={() => {
					show = false;
				}}
			>
				✕
			</button>
		</div>

		<div class="flex flex-col md:flex-row w-full px-5 pb-4 md:space-x-4 dark:text-gray-200">
			<form
				class="flex flex-col w-full"
				on:submit|preventDefault={() => {
					submitHandler();
				}}
			>
				<div class="text-sm font-medium mb-2">{$i18n.t('Default Model Metadata')}</div>
				<div class="flex flex-col gap-2 mb-4">
					<div class="flex items-center justify-between">
						<div class="text-sm">{$i18n.t('Vision')}</div>
						<Switch bind:state={metadata.vision} />
					</div>
					<div class="flex items-center justify-between">
						<div class="text-sm">{$i18n.t('Web Search')}</div>
						<Switch bind:state={metadata.web_search} />
					</div>
					<div class="flex items-center justify-between">
						<div class="text-sm">{$i18n.t('File Upload / Context')}</div>
						<Switch bind:state={metadata.file_upload} />
					</div>
					<div class="flex items-center justify-between">
						<div class="text-sm">{$i18n.t('Code Interpreter')}</div>
						<Switch bind:state={metadata.code_interpreter} />
					</div>
					<div class="flex items-center justify-between">
						<div class="text-sm">{$i18n.t('Builtin Tools')}</div>
						<Switch bind:state={metadata.builtin_tools} />
					</div>
				</div>

				<div class="text-sm font-medium mb-2">{$i18n.t('Default Model Params')}</div>
				<div class="flex flex-col gap-2 mb-4">
					<div class="flex items-center justify-between gap-2">
						<div class="text-sm shrink-0">{$i18n.t('Temperature')}</div>
						<input
							class="w-full text-sm bg-transparent outline-none text-right"
							type="number"
							step="0.1"
							min="0"
							max="2"
							bind:value={params.temperature}
						/>
					</div>
					<div class="flex items-center justify-between gap-2">
						<div class="text-sm shrink-0">{$i18n.t('Top P')}</div>
						<input
							class="w-full text-sm bg-transparent outline-none text-right"
							type="number"
							step="0.05"
							min="0"
							max="1"
							bind:value={params.top_p}
						/>
					</div>
					<div class="flex items-center justify-between gap-2">
						<div class="text-sm shrink-0">{$i18n.t('Max Tokens')}</div>
						<input
							class="w-full text-sm bg-transparent outline-none text-right"
							type="number"
							step="1"
							min="1"
							bind:value={params.max_tokens}
						/>
					</div>
				</div>

				<div class="flex justify-end pt-3">
					<button
						class="px-4 py-2 text-sm font-medium rounded-lg bg-black text-white dark:bg-white dark:text-black"
						type="submit"
						disabled={loading}
					>
						{loading ? $i18n.t('Saving...') : $i18n.t('Save')}
					</button>
				</div>
			</form>
		</div>
	</div>
</Modal>
