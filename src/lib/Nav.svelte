<style>
.frosted {
	backdrop-filter: blur(15px);
	background-color: hsla(0, 0%, 100%, 0.75);
}
.pdl {
	padding-left: 3em;
}
.text-extrasmall {
	font-size: 0.5rem;
}
</style>

<script lang="ts">
import { goto } from '$app/navigation'
import { session } from '$app/stores'
import Cookies from 'universal-cookie'
const cookies = new Cookies()
import { spring } from 'svelte/motion'
import { cart } from '../../store/cart'
import Search from '$lib/Search.svelte'
export let section

const cart_qty = spring()
$: cart_qty.set($cart.qty)
$: offset = modulo($cart_qty, 1)

function modulo(n: number, m: number) {
	// handle negative numbers
	return ((n % m) + m) % m
}
async function logout() {
	try {
		// const res = await post('auth/logout')
		cookies.remove('token')
		$session.user = null
		$session.token = null
		goto('/login')
	} catch (e) {}
}
</script>

<nav class="flex justify-between p-4 shadow-md border-gray-100 frosted">
	<a href="/" class="flex items-center focus:outline-none max-w-max"
		><img alt="" class="w-8 h-8" src="/logo.svg" />
		<div class="">
			<h2 class="ml-2 text-xl font-bold tracking-wide">LITEKART</h2>
			<div class="ml-auto h-0.5 w-10 bg-pink-700"></div>
		</div>
	</a>
	<div
		class="flex items-center justify-center w-full ml-8 text-sm font-semibold tracking-wide uppercase xl:ml-10">
		{#each $session.categories as c}
			<a
				href="{`/search?categories=${c.slug}&page=1`}"
				class="mx-2 transform cursor-pointer hover:scale-95 whitespace-nowrap xl:mx-5">
				{c.name}
			</a>
		{/each}
	</div>
	<!-- <Search /> -->
	<!-- <div class="w-1/2 mx-5">
		<label class="relative flex items-center">
			<input
				type="text"
				placeholder="Search for products..."
				class="w-full bg-transparent py-1.5 pdl focus:outline-none rounded-full border border-gray-800" />
			<div class="absolute ml-4">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="w-5 h-5 "
					viewBox="0 0 20 20"
					fill="currentColor">
					<path
						fill-rule="evenodd"
						d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z"
						clip-rule="evenodd"></path>
				</svg>
			</div>
		</label>
	</div> -->

	<div class="flex items-center">
		<a sveltekit:prefetch href="/cart" class:selected="{section === 'cart'}">
			<button
				class="flex items-center mr-5 font-semibold transform focus:outline-none whitespace-nowrap hover:scale-95">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="w-5 h-5 mr-1"
					fill="none"
					viewBox="0 0 24 24"
					stroke="currentColor">
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"></path>
				</svg>

				<span
					class="counter-digits absolute flex items-center justify-center p-0.5 text-extrasmall text-white bg-black rounded-full w-4 h-4 top-0 right-0 -mr-2 -mt-2"
					style="transform: translate(0, {100 * offset}%)">{Math.floor($cart_qty)}</span>
			</button>
		</a>
		{#if !$session.user || !$session.user.email}
			<a class:selected="{section === 'login'}" sveltekit:prefetch href="/login">
				<button
					class="flex items-center font-semibold transform focus:outline-none whitespace-nowrap hover:scale-95">
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="w-5 h-5 mr-1"
						fill="none"
						viewBox="0 0 24 24"
						stroke="currentColor">
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M11 16l-4-4m0 0l4-4m-4 4h14m-5 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h7a3 3 0 013 3v1"
						></path>
					</svg>
					<span>LOGIN</span>
				</button>
			</a>
		{:else}
			<button on:click="{logout}">
				{$session.user.email}
			</button>
		{/if}
	</div>
</nav>
