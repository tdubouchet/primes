<template>
    <v-app id="home">
        <v-main>
            <router-view/>
        </v-main>
    </v-app>
</template>

<style>
    #home {
        background-image: url(/bg.jpg);
        background-position: center center;
        background-repeat: no-repeat;
        background-attachment: fixed;
        background-size: cover;
        background-color: #fff;
    }
</style>
