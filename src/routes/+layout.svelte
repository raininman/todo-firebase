<script>
	import { onMount } from 'svelte';
	import { auth, db } from '../lib/firebase/firebase.js';
	import { doc, getDoc, setDoc } from 'firebase/firestore';
	import { authStore } from '../store/store.js';

	const nonAuthRoutes = ['/'];
	onMount(() => {
		const unsubscribe = auth.onAuthStateChanged(async (user) => {
			const currPath = window.location.pathname;

			if (!user && !nonAuthRoutes.includes(currPath)) {
				window.location.href = '/';
				return;
			}
			if (user && currPath === '/') {
				window.location.href = '/dashboard';
				return;
			}
			if (!user) {
				return;
			}

			let dataToSetToStore;
			const docRef = doc(db, 'user', user.uid);
			const docSnap = await getDoc(docRef);
			if (!docSnap.exists()) {
				const userRef = doc(db, 'user', user.uid);
				dataToSetToStore = {
					email: user.email,
					todos: ['sample']
				};
				await setDoc(userRef, dataToSetToStore, { merge: true });
			} else {
				const userData = docSnap.data();
				dataToSetToStore = userData;
			}
			authStore.update((curr) => {
				return { ...curr, user, data: dataToSetToStore, loading: false };
			});
		});
	});
</script>

<div class="mainContainer">
	<slot />
</div>

<style>
	.mainContainer {
		min-height: 100vh;
		position: relative;
		display: flex;
		flex-direction: column;
	}
</style>
