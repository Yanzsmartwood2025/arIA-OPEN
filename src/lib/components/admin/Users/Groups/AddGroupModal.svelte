<script lang="ts">
import { createEventDispatcher } from 'svelte';
import Modal from '$lib/components/common/Modal.svelte';

export let show = false;
export let onSubmit: (group: { name: string; description: string }) => void = () => {};

let name = '';
let description = '';

const submitHandler = () => {
if (name.trim() === '') return;
onSubmit({ name, description });
name = '';
description = '';
show = false;
};
</script>

<Modal bind:show size="sm">
<div class="p-5">
<div class="text-lg font-medium mb-4">Create Group</div>
<form
on:submit|preventDefault={submitHandler}
class="flex flex-col gap-3"
>
<div>
<div class="text-sm font-medium mb-1">Name</div>
<input
class="w-full rounded-lg py-2 px-3 text-sm bg-gray-50 dark:bg-gray-850 outline-none"
type="text"
bind:value={name}
placeholder="Group name"
required
/>
</div>
<div>
<div class="text-sm font-medium mb-1">Description</div>
<textarea
class="w-full rounded-lg py-2 px-3 text-sm bg-gray-50 dark:bg-gray-850 outline-none"
bind:value={description}
placeholder="Group description"
/>
</div>
<div class="flex justify-end gap-2 mt-2">
<button
type="button"
class="px-3 py-1.5 text-sm rounded-lg bg-gray-100 dark:bg-gray-800"
on:click={() => (show = false)}
>
Cancel
</button>
<button
type="submit"
class="px-3 py-1.5 text-sm rounded-lg bg-black text-white dark:bg-white dark:text-black"
>
Create
</button>
</div>
</form>
</div>
</Modal>
