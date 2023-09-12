<template>
    <v-card>
        
      <v-layout>
        <v-navigation-drawer
          expand-on-hover
          
          rail
        >
          <v-divider></v-divider>
          <v-list density="compact" nav>
            
            <v-list-item router-link to="/Home" prepend-icon="mdi-home" title="Home" value="home"></v-list-item>
            <v-list-item router-link to="/ImagenVista" prepend-icon="mdi-image" title="Imagen" value="image"></v-list-item>
            <v-list-item router-link to="/TablaVista" prepend-icon="mdi-table" title="Tabla" value="table"></v-list-item>
          </v-list>
        </v-navigation-drawer>
  
        <v-main style="height: 860px">
            <components>
                <Imagen></Imagen>
            </components>
        </v-main>
      </v-layout>
    </v-card>
  </template>
  
  
  
  
  <script setup>
    import Imagen from '@/components/Imagen.vue';
    
  </script>
  