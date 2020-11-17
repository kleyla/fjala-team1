<template>
  <v-container fluid>
    <v-col class="mb-6 card__form">
      <PedidoRegisterFormCard />
    </v-col>
  </v-container>
</template>

<script>
import PedidoRegisterFormCard from "@/components/pedido/PedidoRegisterFormCard";

export default {
  name: "RegistrarPedido",
  components: { PedidoRegisterFormCard },
};
</script>

<style scoped>
.card__form {
  margin: 0 auto;
}
</style>