<template>
  <v-app>
      <!-- <v-app-bar app>
           <v-toolbar-title>Compra</v-toolbar-title>
           </v-app-bar> -->
    <v-main>
    <v-container>
      <v-form ref="form" v-model="valid">
        <v-text-field
          v-model="purchase.date"
          label="Fecha"
          type="date"
          :rules="[rules.required]"
          required
        ></v-text-field>
        <v-select
          v-model="purchase.customer"
          :items="clients"
          item-title="name"
          item-value="id"
          label="Cliente"
          :rules="[rules.required]"
          required
        ></v-select>
        <v-textarea
          v-model="purchase.notes"
          label="Notas"
        ></v-textarea>
        <v-divider></v-divider>
        <v-container>
          <v-toolbar>
          <v-toolbar-title secondary>Productos</v-toolbar-title>
        </v-toolbar>
        <div v-for="(line, index) in purchase.saleline_set" :key="line.id">
          <v-row>
            <v-col>
              <v-select
                v-model="line.product"
                :items="products"
                item-title="name"
                item-value="id"
                label="Producto"
                :rules="[rules.required]"
                required
              ></v-select>
            </v-col>
            <v-col>
              <v-text-field
                v-model.number="line.unit_price"
                label="Precio"
                type="number"
                :rules="[rules.required]"
                required
              ></v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model.number="line.quantity"
                label="Cantidad"
                type="number"
                :rules="[rules.required]"
                required
              ></v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                :value="calculateSubtotal(line)"
                label="Subtotal"
                readonly
              ></v-text-field>
            </v-col>
            <v-col>
              <v-btn @click="removeLine(index)" color="red">Eliminar</v-btn>
            </v-col>
          </v-row>
        </div>
        <v-btn @click="addLine" color="blue">Agregar</v-btn>

      </v-container>

        <v-divider></v-divider>

        <v-text-field
          :value="calculateTotal"
          label="Total"
          readonly
        ></v-text-field>
        <v-btn @click="submit" color="green">Comprar</v-btn>
      </v-form>
    </v-container>
    </v-main>
  </v-app>
</template>

<script>
 export default {
     name: 'DonConfiao',
     props: {
         msg: String
     },
     data() {
         return {
             valid: false,
             purchase: {
                 date: '',
                 client: null,
                 notes: '',
                 saleline_set: [{product:'', unit_price: 0, quantity: 0}],
             },
             rules: {
                 required: value => !!value || 'Requerido.',
             },
             clients: [],
             products: [],
         };
     },
     created() {
         this.fetchClients();
         this.fetchProducts();
     },
     computed: {
         calculateTotal() {
             return this.purchase.saleline_set.reduce((total, saleline) => {
                 return total + this.calculateSubtotal(saleline);
             }, 0);
         },
     },
     methods: {
         fetchClients() {
             fetch('/don_confiao/api/customers/')
                 .then(response => response.json())
                 .then(data => {
                     this.clients = data;
                 })
                 .catch(error => {
                     console.error(error);
                 });
         },
         fetchProducts() {
             fetch('/don_confiao/api/products/')
                 .then(response => response.json())
                 .then(data => {
                     console.log(data);
                     this.products = data;
                 })
                 .catch(error => {
                     console.error(error);
                 });
         },
         addLine() {
             this.purchase.saleline_set.push({ product: '', unit_price: 0, quantity:0 });
         },
         removeLine(index) {
             this.purchase.saleline_set.splice(index, 1);
         },
         calculateSubtotal(line) {
             return line.unit_price * line.quantity;
         },
         async submit() {
             if (this.$refs.form.validate()) {
                 try {
                     const response = await fetch('/don_confiao/api/sales/', {
                         method: 'POST',
                         headers: {
                             'Content-Type': 'application/json',
                         },
                         body: JSON.stringify(this.purchase),
                     });
                     if (response.ok) {
                         const data = await response.json();
                         console.log('Compra enviada:', data);
                         this.$router.push("SummaryPurchase");
                     } else {
                         console.error('Error al enviar la compra:', response.statusText);
                     }
                 } catch (error) {
                     console.error('Error de red:', error);
                 }
             }
         },
         navigate(route) {
             this.$router.push(route);
         },
     },
 };
</script>

<style>
</style>
