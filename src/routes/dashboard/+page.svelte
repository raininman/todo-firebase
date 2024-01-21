<script>
	import { authHandler, authStore } from '../../store/store';
	import { doc, getDoc, setDoc } from 'firebase/firestore';
	import { auth, db } from '../../lib/firebase/firebase.js';

	const pen = 'pen.svg';
	const trash = 'trash.svg';

	let todoList = ['sample'];
	let currTodo = '';
	let error = false;

	authStore.subscribe((curr) => {
		todoList = curr.data.todos;
	});

	function addTodo() {
		if (!currTodo) {
			error = true;
			return;
		}
		todoList = [...todoList, currTodo];
		currTodo = '';
	}

	function editTodo(index) {
		let newTodoList = todoList.filter((todo, i) => {
			return i !== index;
		});
		currTodo = todoList[index];
		todoList = [...newTodoList];
	}

	function removeTodo(index) {
		let newTodoList = todoList.filter((todo, i) => {
			return i !== index;
		});
		todoList = [...newTodoList];
	}

	async function saveTodo() {
		try {
			const userRef = doc(db, 'user', $authStore.user.uid);
			await setDoc(
				userRef,
				{
					todos: todoList
				},
				{ merge: true }
			);
		} catch (error) {
			console.log(`Error ${error}`);
		}
	}
</script>

{#if !$authStore.loading}
	<div class="mainContainer">
		<header>
			<h1>Todo List</h1>
			<div class="headerButtons">
				<button on:click={saveTodo}>Save</button>
				<button on:click={authHandler.logout}>Logout</button>
			</div>
		</header>
		<main>
			{#if todoList.length === 0}
				<p>You have nothing to do</p>
			{/if}
			{#each todoList as todo, index}
				<div class="todo">
					<p>{index + 1}. {todo}</p>
					<div class="actions">
						<!-- svelte-ignore a11y-click-events-have-key-events -->
						<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
						<img
							src={pen}
							alt="change"
							height="20"
							width="20"
							on:click={() => editTodo(index)}
							onkeydown={() => {}}
						/>
						<!-- svelte-ignore a11y-click-events-have-key-events -->
						<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
						<img
							src={trash}
							alt="delete"
							height="20"
							width="20"
							on:click={() => removeTodo(index)}
							onkeydown={() => {}}
						/>
					</div>
				</div>
			{/each}
		</main>
		<div class="enterTodo">
			<input
				type="text"
				placeholder="Enter Todo"
				bind:value={currTodo}
				class={error ? 'error' : ''}
			/>
			<button on:click={addTodo}>Add</button>
		</div>
	</div>
{/if}

<style>
	.mainContainer {
		min-height: 100vh;
		display: flex;
		flex-direction: column;
		gap: 24px;
		width: 100%;
		max-width: 1000px;
		margin: 0 auto;
	}
	header {
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.headerButtons {
		display: flex;
		align-items: center;
		gap: 16px;
	}

	main {
		display: flex;
		flex-direction: column;
		gap: 8px;
		flex: 1;
	}

	.todo {
		border-left: 1px solid var(--primary);
		padding: 4px 8px;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.actions {
		display: flex;
		align-items: center;
		gap: 8px;
	}

	.actions img:hover {
		cursor: pointer;
	}

	.todo p {
		margin-bottom: 0;
	}

	.enterTodo {
		display: flex;
		align-items: stretch;
	}
	.enterTodo button {
		max-width: 100px;
	}

	.error {
		border: crimson;
	}
</style>
