<template>
  <div>
    <VCard title="Create Awesome 🙌2">
      <VCardText>This is your second page.</VCardText>
      <VCardText>
        Chocolate sesame snaps pie carrot cake pastry pie lollipop muffin.
        Carrot cake dragée chupa chups jujubes. Macaroon liquorice cookie
        wafer tart marzipan bonbon. Gingerbread jelly-o dragée
        chocolate.
      </VCardText>
      
      <VBtn  @click="add">add</VBtn>
      <p class="fw-bold">{{ count }}</p>
      <VBtn  @click="min">min</VBtn>

      </VCard>
  </div>
</template>


<script setup>
import { ref } from 'vue';

let count = ref(0);

let add = () => {
  count.value++;
}

const min = ()=> {
  count.value--;
}


</script>


