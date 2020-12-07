<template>
	<div>
		<header class="bg-blue-500 py-10 px-3 items-center">
			<div class="md:flex md:justify-center items-center text-center">
				<img src="../assets/logo.png" class="inline-block align-middle h-10 w-10 md:h-12 md:w-12" alt="">
				<h2 class="ml-2 text-white text-2xl font-semibold md:text-3xl md:font-extrabold ">Vue.js Poster Shop</h2>
			</div>
			<div class="mt-3">
				<form class="w-full mx-auto lg:w-1/2 flex justify-center" @submit.prevent="onSubmit">
					<input type="text" class="md:w-2/3 py-3 px-3 rounded-l placeholder-gray-700" v-model="search" @keyup.enter="onSubmit" placeholder="Search" required>
					<button class="bg-yellow-500 py-3 px-2 rounded-r" type="submit">Search</button>
				</form>
			</div>
			<p class="mt-6 lg:mt-4 flex justify-center text-white"> Find items with key words <strong class="pl-1"> <em> Cat,dog,flower</em></strong> </p>
		</header>
		<main class="bg-gray-200 space-y-8" :class="{ 'h-screen' : !products.length, 'h-full' : products.length > 0}">
			<div class="flex flex-col-reverse md:w-10/12 md:mx-auto md:flex md:flex-row pt-8">
				<div class="w-full px-6 md:w-1/2">
					<p class="py-6 font-bold ">
						Found {{ products.length }} in the search term {{ lastSearch }}
					</p>
					<div v-for="(product , id) in products" :key=id class="bg-white py-2 px-4 md:py-6 md:px-12 mb-6 shadow rounded">
						<div class="lg:flex">
							<div>
								<img :src="product.thumb" class="px-2 py-3 md:mr-6 md:py-4 md:w-64 md:h-48" alt="">
							</div>
							<div class="space-y-2 mb-2 md:flex md:flex-col text-center md:justify-center ">
								<p class="md:py-2">{{ product.title }}</p>
								<p class="md:py-2 text-blue-700 md:text-xl font-bold">{{ product.price | totally }}</p>
								<button @click="addToCart(product)" class="text-sm px-2 py-1 bg-blue-500 text-white md:px-4 rounded">Add to cart</button>
							</div>
						</div>
					</div>
				</div>
				<div class="w-full px-10 md:w-1/2 flex flex-col text-gray-800">
					<div class="border-b border-gray-500 mb-4 mt-6">
						<h2 class="uppercase font-bold lg:text-2xl pb-2">
							shopping cart
						</h2>
						<ul>
							<li v-for="(item , id ) in cart" :key="id" class="py-2">
								{{ item.title }}
								<p class="py-2">{{ item.price | totally }} x {{ item.quantity }} |
									<button @click="inc(item)" class="font-bold ml-2 bg-blue-500 px-2 text-xs hover:text-white rounded">+</button>
									<button @click="dec(item)" class="font-bold ml-2 bg-blue-500 px-2 text-xs hover:text-white rounded">&minus;</button>
								</p>
							</li>
						</ul>
					</div>
					<span v-if="cart.length" class="font-semibold text-lg text-right">{{ total | totally }}</span>
					<span v-if="!cart.length" class="font-semibold lg:text-lg"> No item in the Cart </span>
				</div>
			</div>
			<div class="bg-gray-900 py-4 flex justify-center text-gray-400 text-sm sm:text-lg">
				<div>
					<span>Simple Cart &copy; {{ date.getFullYear('Y') }} . All rights Preserved. Developed by </span>
					<a href="https://github.com/Ngizeh">Edward Mwangi</a>
				</div>
			</div>
		</main>
	</div>
</template>

<script>
	import axios from 'axios';
	export default {
		name: 'Home',
		data() {
			return {
				products : [],
				total : 0,
				cart : [],
				search : 'Cat',
				lastSearch : '',
				date : new Date
			}
		},
		methods : {
			addToCart(product){
				this.total += product.price;
				let found = false;
				for (let i = 0; i < this.cart.length; i++)
				{
					if(this.cart[i].id === product.id){
						this.cart[i].quantity++ ;
						found = true;
					}
				}
				if (!found)
				{
					this.cart.push({
						id : product.id,
						title : product.title,
						price : product.price,
						quantity : 1,
					})
				}
				scrollTo(0, 300);
			},
			inc(item){
				item.quantity++
				this.total += item.price;
				if(item.quantity == 0){
					return this.total = 0
				}
			},
			dec(item){
				item.quantity--
				this.total -= item.price;
				if(item.quantity == 0){
					return this.cart.pop()
				}
			},
			onSubmit(){
				var app = this;
				app.products = [];
				app.lastSearch = app.search;
				axios('/search.json').then(function(response) {
					const products = response.data;
					app.products = products.filter((product) => product.title.includes(app.lastSearch))
				});
			}
		},
		filters : {
			totally(price) {
				return `Ksh. ${price.toFixed(2)}`
			}
		},
		created() {
			this.onSubmit()
		}
	}
</script>

<style src="../assets/tailwind.css">
</style>
