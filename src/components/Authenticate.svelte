<script>
	import { authHandler } from '../store/store';

	const loading = '/loader.gif';

	let email = '';
	let password = '';
	let confirmPassword = '';
	let error = false;
	let register = false;
	let authenticating = false;

	async function handleAuth() {
		if (authenticating) {
			return;
		}

		if (!email || !password || (register && (confirmPassword !== password))) {
			error = true;
			return;
		}

		authenticating = true;

		try {
			if (!register) {
				await authHandler.login(email, password);
			} else {
				await authHandler.signup(email, password);
			}
		} catch (error) {
			console.log(`Error: ${error}`);
			error = true;
      authenticating =false
		}
	}

	function handleRegister() {
		register = !register;
	}
</script>

<div class="authContainer">
	<form action="">
		<h1>{register ? 'Register' : 'Login'}</h1>
		{#if error}
			<p class="error">The info isn't correct</p>
		{/if}

		<label>
			<p class={email ? 'above' : 'center'}>Email</p>
			<input type="email" placeholder="Email" bind:value={email} />
		</label>
		<label>
			<p class={password ? 'above' : 'center'}>Password</p>
			<input type="password" placeholder="Password" bind:value={password} />
		</label>
		{#if register}
			<label>
				<p class={confirmPassword ? 'above' : 'center'}>Confirm Password</p>
				<input type="password" placeholder="Confirm Password" bind:value={confirmPassword} />
			</label>
		{/if}
		<button type="submit" on:click={handleAuth}>
			{#if authenticating}
				<img src={loading} alt="loading" height="20" width="20" />
			{:else}
				Submit
			{/if}
		</button>
	</form>

	<div class="options">
		<p>Or</p>
		{#if register}
			<div>
				<p>Already have an account?</p>
				<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
				<p on:click={handleRegister} on:keydown={() => {}}>Login</p>
			</div>
		{:else}
			<div>
				<p>Don't have an account?</p>
				<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
				<p on:click={handleRegister} on:keydown={() => {}}>Register</p>
			</div>
		{/if}
	</div>
</div>

<style>
	.authContainer {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		flex: 1;
	}

	form {
		display: flex;
		flex-direction: column;
	}

	form,
	.options {
		width: 400px;
		max-width: 100%;
		margin: 0 auto;
	}

	form input {
		width: 100%;
	}
	form h1 {
		text-align: center;
	}

	.above,
	.center {
		position: absolute;
		transform: translateY(-50%);
		pointer-events: none;
		border-radius: 4px;
		padding: 0 6px;
	}

	.above {
		background: var(--background-color);
		border: 1px solid var(--background-color);
	}

	.center {
		top: 50%;
		left: 6px;
		border: 1px solid transparent;
		opacity: 0;
	}

	.error {
		color: crimson;
	}

	.options {
		padding: 14px 0;
		overflow: hidden;
		display: flex;
		flex-direction: column;
		gap: 4px;
	}

	.options > p {
		position: relative;
		text-align: center;
		width: fit-content;
		margin: 0 auto;
		padding: 0 8px;
	}
	.options > p::after,
	.options > p::before {
		position: absolute;
		content: '';
		top: 50%;
		transform: translateY(-50%);
		width: 100vw;
		height: 1.5px;
		background: whitesmoke;
	}

	.options > p::after {
		right: 100%;
	}

	.options > p::before {
		left: 100%;
	}

	.options div {
		display: flex;
		align-items: center;
		gap: 8px;
	}

	.options div p:last-of-type {
		color: var(--primary);
		cursor: pointer;
	}
</style>
