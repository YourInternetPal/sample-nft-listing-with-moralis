<template>
  <nav class="bg-gray-800">
    <div class="max-w-7xl mx-auto px-2 sm:px-6 lg:px-8">
      <div class="relative flex items-center justify-between h-16">
        <div class="flex-1 flex items-center justify-center sm:items-stretch sm:justify-start">
        </div>
        
      </div>
    </div>
  </nav>

  <div class="bg-white">
      <div class="max-w-2xl mx-auto py-16 px-4 sm:py-24 sm:px-6 lg:max-w-7xl lg:px-8">
        <h2 class="text-2xl font-extrabold tracking-tight text-gray-900">NFT Listings with Moralis</h2>
    
        <div class="mt-6 grid grid-cols-1 gap-y-10 gap-x-6 sm:grid-cols-2 lg:grid-cols-4 xl:gap-x-8"  >
          <div class="group relative" v-for="nft in nfts">
            <div class="w-full min-h-80 bg-gray-200 aspect-w-1 aspect-h-1 rounded-md overflow-hidden group-hover:opacity-75 lg:h-80 lg:aspect-none">
              <img :src="JSON.parse(nft.metadata).image.replace('ipfs://', 'https://ipfs.io/ipfs/')" alt="Front of men&#039;s Basic Tee in black." class="w-full h-full object-center object-cover lg:w-full lg:h-full">
            </div>
            <div class="mt-4 flex justify-between" >
              <div>
                <h3 class="text-sm text-gray-700">
                  <a href="#">
                    <span aria-hidden="true" class="absolute inset-0"></span>
                    {{ nft.metadata_name }}
                  </a>
                </h3>
                
              </div>
              <!-- <p class="text-sm font-medium text-gray-900">$35</p> -->
            </div>
          </div>
    
          <!-- More products... -->
        </div>
      </div>
  </div>



</template>

<script lang="ts" setup>
import { ref, onMounted, computed } from "vue";
import Moralis from "moralis";

const serverUrl = import.meta.env.VITE_MORALIS_SERVER_URL;
const appId = import.meta.env.VITE_MORALIS_APPLICATION_ID;
Moralis.start({ serverUrl, appId });

const user = ref<Moralis.User<Moralis.Attributes> | void | null>(null);
const isAuthenticated = computed<boolean>(() => user.value !== null);

let nfts:any = ref([]);

onMounted(() => {
  if(!isAuthenticated.value) {
    user.value = Moralis.User.current();
    
  }
  fetchNfts();
});

const logIn = async () => {
  if (!isAuthenticated.value) {
    user.value = await Moralis.authenticate({
      signingMessage: "Log in using Moralis",
    })
      .then((user) => {
        fetchNfts();
      })
      .catch((error) => {
        console.error(error);
      });
  }
};

const fetchNfts = async() => {
  const options:any = { q: "Bored Ape Tokyo", chain: "Eth", filter: "name", limit: "10" };
  const resp = await Moralis.Web3API.token.searchNFTs(options);
  nfts.value = resp.result;
}



</script>

<style scoped></style>
