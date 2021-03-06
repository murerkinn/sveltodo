<script>
	import { onMount } from 'svelte';
	import { items } from '../stores';
	import ToDoAPI from '../todo-api';
	import Item from './Item.svelte';
	import NewItem from './NewItem.svelte';
	import { v4 } from 'uuid';

	function handleAddNewItem(e) {
		$items = [
			{
				id: v4(),
				text: e.detail,
				completed: false,
			},
			...$items,
		];

		ToDoAPI.save($items);
	}

	function handleUpdateItem(e) {
		const index = $items.findIndex((item) => item.id === e.detail.id);
		$items[index] = e.detail;
		ToDoAPI.save($items);
	}

	function handleDeleteItem(e) {
		$items = $items.filter((item) => item.id !== e.detail);
		ToDoAPI.save($items);
	}

	onMount(async () => {
		$items = await ToDoAPI.getAll();
	});
</script>

<div class="list">
	<NewItem on:newitem={handleAddNewItem} />
	{#each $items as item (item)}
		<Item {...item} on:update={handleUpdateItem} on:delete={handleDeleteItem} />
	{:else}
		<p class="list-status">No Items Exist</p>
	{/each}
</div>

<style>
	.list {
		padding: 15px;
	}

	.list-status {
		margin: 0;
		text-align: center;
		color: #fff;
		font-weight: bold;
		font-size: 1.1em;
	}
</style>
