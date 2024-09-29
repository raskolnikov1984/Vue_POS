<template>
    <v-navigation-drawer expand-on-hover rail app>
        <v-list-item title="Menu" subtitle="Tienda"></v-list-item>
        <v-divider></v-divider>
        <v-list>
            <v-list-item v-for="item in menuItems" :key="item.title" @click="navigate(item.route)">
                <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item>
        </v-list>
    </v-navigation-drawer>
</template>

<script>
 export default {
     name: 'NavBar',
     rail: true,
     data () {
         return {
             menuItems: [
                 { title: 'Inicio', route: '/'},
                 { title: 'Comprar', route:'/comprar'},
                 { title: 'Resumen de Compra', route:'/resumen_compra'},
             ],

         };
     },
     methods: {
         navigate(route) {
             this.$router.push(route);
         },
     }

 }
</script>
