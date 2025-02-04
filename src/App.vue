<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
const name = ref('');
const pickupNumber = ref('');
const weight = ref('');
const price = ref('');
const phone = ref('');
const email = ref('');
const paid = ref(false);
const orders = ref<IOrder[]>([]);
const showForm = ref(false); // Toggle form visibility

const isDesktop = computed(() => window.innerWidth>560)

onMounted(() => {
  const savedOrders = localStorage.getItem('orders');
  if (savedOrders) orders.value = JSON.parse(savedOrders);
  showForm.value = isDesktop.value
});

type IOrder = {
  name: string,
  pickupNumber: string,
  weight: string,
  price: string,
  phone: string,
  email: string,
  paid: boolean
}

const setOrder = (o: IOrder) => {
  name.value = o.name
  pickupNumber.value = o.pickupNumber
  weight.value = o.weight
  price.value = o.price
  phone.value = o.phone
  email.value = o.email
  paid.value = o.paid
}

const removeOrder = (index: number) => {
  orders.value.splice(index, 1);
  localStorage.setItem('orders', JSON.stringify(orders.value));
};

const addOrder = () => {
  if (!name.value || !pickupNumber.value || !weight.value || !price.value) {
    alert('Vul alle verplichte velden in!');
    return;
  }

  const newOrder: IOrder = {
    name: name.value,
    pickupNumber: pickupNumber.value,
    weight: weight.value,
    price: price.value,
    phone: phone.value,
    email: email.value,
    paid: paid.value,
  };

  orders.value.push(newOrder);
  localStorage.setItem('orders', JSON.stringify(orders.value));

  // Reset fields
  name.value = '';
  pickupNumber.value = '';
  weight.value = '';
  price.value = '';
  phone.value = '';
  email.value = '';
  paid.value = false;
};
</script>

<template>
  <div class="flex flex-col sm:flex-row gap-12 max-w-[85vw]">
    <div class="flex flex-col">
      <img class="relative lg:fixed lg:opacity-25 opacity-75 -z-10 w-30 md:w-80 lg:w-100" src="/src/assets/Washing-machine.png" alt="wahing" />
      <h2 class="text-2xl font-bold mb-4 text-center">Wasserij Orderformulier</h2>
      <!-- Toggle Button -->
      <button @click="showForm = !showForm" v-if="!isDesktop"
        class="w-full bg-blue-500 text-white py-2 rounded-lg hover:bg-blue-600 mb-4">
        {{ showForm ? 'Sluit Formulier' : 'Voeg Order Toe' }}
      </button>
      <transition name="expand">
        <div v-show="showForm" class="overflow-hidden">
          <form @submit.prevent="addOrder" class="flex flex-col gap-2">
            <label class="font-semibold">Naam:</label>
            <input v-model="name" class="border p-2 rounded-lg" type="text" required />

            <label class="font-semibold">Afhaalnummer:</label>
            <input v-model="pickupNumber" class="border p-2 rounded-lg" type="text" required />

            <label class="font-semibold">Gewicht (kg):</label>
            <input v-model="weight" class="border p-2 rounded-lg" type="number" required />

            <label class="font-semibold">Prijs (€):</label>
            <input v-model="price" class="border p-2 rounded-lg" type="number" required />

            <label class="font-semibold">Telefoon:</label>
            <input v-model="phone" class="border p-2 rounded-lg" type="text" />

            <label class="font-semibold">E-mail:</label>
            <input v-model="email" class="border p-2 rounded-lg" type="email" />

            <div class="flex gap-5 my-2">
              <label class="font-semibold">Betaald:</label>
              <input v-model="paid" type="checkbox" />
            </div>

            <button type="submit">Toevoegen</button>
          </form>
        </div>
      </transition>
    </div>
    <div v-if="orders.length" class="overflow-x-auto">
      <h3 class="text-lg font-bold mb-2">Bestellingen</h3>
      <table class="min-w-full border border-gray-300">
        <thead>
          <tr>
            <th class="py-2 px-4 border-b">Naam</th>
            <th class="py-2 px-4 border-b">Pickup Number</th>
            <th class="py-2 px-4 border-b">Weight</th>
            <th class="py-2 px-4 border-b">Prijs</th>
            <th class="py-2 px-4 border-b">Email</th>
            <th class="py-2 px-4 border-b">Phone</th>
            <th class="py-2 px-1 border-b"></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(o,i) in orders" :key="o.name" class="hover:bg-gray-500" @click.prevent="setOrder(o)">
            <td class="py-2 px-4 border-b">{{ o.name }}</td>
            <td class="py-2 px-4 border-b">{{ o.pickupNumber }}</td>
            <td class="py-2 px-4 border-b">{{ o.weight }}</td>
            <td class="py-2 px-4 border-b">{{ o.price }}</td>
            <td class="py-2 px-4 border-b">{{ o.email }}</td>
            <td class="py-2 px-4 border-b">{{ o.phone }}</td>
            <td class="py-2 px-1 border-b"><i class="cursor-pointer" @click.stop="removeOrder(i)">❌</i></td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
<style>
.expand-enter-active,
.expand-leave-active {
  transition: max-height 0.5s ease-in-out, opacity 0.3s ease-in-out;
  overflow: hidden;
}

.expand-enter-from,
.expand-leave-to {
  max-height: 0;
  opacity: 0;
}

.expand-enter-to,
.expand-leave-from {
  max-height: 500px;
  /* Adjust based on form height */
  opacity: 1;
}
</style>
