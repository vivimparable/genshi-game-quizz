<template>
<div>
   <h2 class="text-center">¡Choose the right character of genshin Impact!</h2>
   <p class="text-center">App by Javier Garcia Urruchi.</p>

    <characters v-on:characters='characters' />

</div>
 
 
</template>

<script lang="ts">

import characters from '@/components/characters.vue'
export default {
  name: 'IndexPage',
  components:{
characters
  },
  data(){
    return{
   
    }
  },methods:{
    characters(a:Number){ 

      try {
        
      } catch (error) {
        
      }
      
     
    }
 
  },computed:{
    
  },created(){
    
  }
}
</script>
