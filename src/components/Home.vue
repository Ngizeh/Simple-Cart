<template>
  <div>
    <header class="bg-blue-500 py-12 px-3 items-center">
         <div class="flex items-center justify-center mx-auto">
            <img src="../assets/logo.png" class="h-12 w-12" alt="">
            <h2 class="ml-2 text-white text-3xl font-extrabold ">Vue.js Poster Shop</h2>
        </div>
        <div class="mt-3 flex justify-center">
            <form class="w-1/2 -mr-32" @submit.prevent="onSubmit">
                <input type="text" class="w-2/3 py-3 px-3 rounded-l placeholder-gray-700" v-model="search" @keyup.enter="onSubmit" placeholder="Search" required>
                <button class="bg-yellow-500 py-3 px-2 rounded-r" type="submit">Search</button>
            </form>
        </div>
        <p class="mt-4 flex justify-center text-white font-"> Find items with key words <strong class="pl-1"> <em> Cat,dog,flower</em></strong> </p>
    </header>
    <main class="bg-gray-200" :class="{ 'h-screen' : !products.length, 'h-full' : products.length > 0}">
        <div class="w-10/12 mx-auto flex justify-between pt-8"> 
           <!-- <div v-if="loading">
              Loading....
           </div> -->
            <div class="w-1/2">
                <p class="py-6 font-bold ">
                    Found {{ products.length }} in the search term {{ lastSearch }}
                </p>
              <div v-for="(product , id) in products" :key=id class="bg-white py-6 px-12 mb-6 shadow rounded">
                    <div class="flex">
                        <div>
                            <img :src="product.thumb" class="py-4 w-64 h-48" alt="">
                        </div>
                        <div class="ml-8 flex flex-col items-left justify-around">
                            <p class="py-2">{{ product.title }}</p>
                            <p class="py-2 text-blue-700 text-xl font-bold">{{ product.price | totally }}</p>
                            <button @click="addToCart(product)" class="bg-blue-500 text-white px-4 py-1 rounded">Add to cart</button>
                        </div>
                            
                    </div>
              </div>
            </div>
            <div class="w-2/5 flex flex-col text-gray-800">
                <div class="border-b border-gray-500 mb-4">
                    <h2 class="uppercase font-bold text-2xl pb-2">
                            shopping cart
                    </h2>
                    <ul>
                       <li v-for="(item , id ) in cart" :key="id" class="py-2">
                           {{ item.title }}
                           <p class="py-2">{{ item.price | totally }} x {{ item.quantity }} |  
                               <button @click="inc(item)" class="font-bold ml-2 bg-blue-500 px-2 text-xs rounded">+</button>
                               <button @click="dec(item)" class="font-bold ml-2 bg-blue-500 px-2 text-xs rounded">&minus;</button>
                          </p>
                        </li>
                    </ul>
                </div>
                <span v-if="cart.length" class="font-semibold text-lg text-right">{{ total | totally }}</span>
                <span v-if="!cart.length" class="font-semibold text-lg"> No item in the Cart </span>
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
        // loading : false
    }
},
methods : {
    addToCart(product)
        {
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
            app.lastSearch = app.search 
            // app.loading = true;
             axios('/search.json')
                .then(function(response) {
                    const products = response.data
                     app.products = products.filter((product) => product.title.includes(app.lastSearch))
                    //  app.loading = false;
            })
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
