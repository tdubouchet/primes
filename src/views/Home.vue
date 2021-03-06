<template>
    <v-card max-width="800" class="mx-auto mt-5 pt-5" elevation="0" color="transparent">
        <v-toolbar color="transparent" flat dense>
            <v-toolbar-title>
                <span class="subheading white--text">Calculateur de primes 🚀</span>
            </v-toolbar-title>

            <v-spacer></v-spacer>

            <CreateBonusModal v-on:addBonus="addBonus"></CreateBonusModal>
        </v-toolbar>


        <v-card-text>
            <v-row>
                <v-col cols="12" class="text-center">
                    <v-chip style="display:block;font-size: 25px" color="white" x-large label v-if="bonuses.length">
                        <span>Prime : <b class="primary--text">{{ totalBonus | currency }}</b> brut 💰</span>
                    </v-chip>

                    <h2 class="text-right white--text" v-else>
                        Commencez par ajouter une prime 😁⬆️
                    </h2>
                </v-col>

                <v-col>
                    <v-card elevation="15" class="mt-3" v-for="(bonus, index) in bonuses" :key="`bonus_${index}`">
                        <v-toolbar flat dense>
                            <v-toolbar-title>
                                <span class="subheading text-uppercase">{{ bonus.name }}</span>
                            </v-toolbar-title>
                            <v-spacer></v-spacer>

                            <v-btn dark icon>
                                <v-icon>mdi-dots-vertical</v-icon>
                            </v-btn>

                            <v-btn icon color="primary" @click="editBonus(bonus)">
                                <v-icon>mdi-cogs</v-icon>
                            </v-btn>

                            <v-btn icon color="red" @click="deleteBonus(index)">
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                        </v-toolbar>

                        <v-card-text>
                            <v-row class="mb-4" justify="space-between">
                                <v-col class="text-left">
                                    <span class="display-3 font-weight-light">
                                        {{ bonus | icon }}
                                        <span v-if="bonus.type === 'percentage'">
                                            {{ bonus.value | currency }}
                                        </span>
                                        <span v-else>{{ bonus.value }}</span>
                                    </span>
                                </v-col>
                            </v-row>

                            <v-slider v-model="bonus.value" color="#9cc42f" track-color="grey" always-dirty min="0" :max="bonus.maxValue">
                                <template v-slot:prepend>
                                    <v-icon color="red" @click="bonus.value --">
                                        mdi-minus
                                    </v-icon>
                                </template>

                                <template v-slot:append>
                                    <v-icon color="primary" @click="bonus.value ++">
                                        mdi-plus
                                    </v-icon>
                                </template>
                            </v-slider>

                            <v-row v-show="bonus.edit">
                                <v-col cols="12" md="6">
                                    <v-text-field dense :label="bonus.name" hide-details v-model="bonus.value" outlined type="number" :max="bonus.maxValue"></v-text-field>
                                </v-col>
                                <v-col cols="12" md="6">
                                    <v-text-field dense label="Valeur max" hide-details v-model="bonus.maxValue" outlined type="number"></v-text-field>
                                </v-col>

                                <v-col cols="12">
                                    <v-text-field dense label="Prime" hide-details v-model="bonus.bonusValue" outlined type="number"></v-text-field>
                                </v-col>
                            </v-row>
                        </v-card-text>
                    </v-card>
                </v-col>
            </v-row>
        </v-card-text>
    </v-card>
</template>

<script>

import {filter, get} from "lodash";
import CreateBonusModal from "@/components/CreateBonusModal";

export default {
    name: 'home',

    components : {
        CreateBonusModal
    },

    data: () => {
        return {
            bonuses : [],
        }
    },

    computed : {
        totalBonus() {
            let totalBonus = 0;

            filter(this.bonuses, ['type', 'percentage']).forEach(bonus => {
                totalBonus += get(bonus, 'value', 0) * (bonus.bonusValue / 100);
            });

            filter(this.bonuses, ['type', 'euro']).forEach(bonus => {
                totalBonus += get(bonus, 'value', 0) * bonus.value;
            });

            return totalBonus.toFixed(2);
        }
    },

    methods : {
        addBonus(bonus) {
            this.bonuses.push(bonus);
        },

        editBonus(bonus) {
            this.$set(bonus, 'edit', !bonus.edit);
        },

        deleteBonus(index) {
            this.$delete(this.bonuses, index);
        }
    },

    watch : {
        bonuses : function() {
            let params = btoa(JSON.stringify(this.bonuses));

            this.$router.push({
                query:  {
                    p : params
                }
            })
        }
    },

    filters : {
        currency(value) {
            return new Intl.NumberFormat('fr-FR', { style: 'currency', currency: 'EUR' }).format(value)
        },

        icon(bonus) {
            let percentage = (bonus.value * 100) / bonus.maxValue;

            if(!isNaN(percentage)) {
                percentage = percentage.toFixed(0);
            } else {
                percentage = 0;
            }

            percentage = parseInt(percentage);

            switch (true) {
                case percentage < 20 :
                    return "💩"
                case percentage >= 20 && percentage < 40 :
                    return "😂"
                case percentage >= 40 && percentage < 60 :
                    return "🙂"
                case percentage >= 60 && percentage < 80 :
                    return "😎"
                case percentage >= 80 && percentage < 100 :
                    return "😱"
                case percentage === 100 :
                    return "🚀"
            }
        }
    },

    beforeMount() {
        try {
            this.bonuses = JSON.parse(atob(this.$route.query.p || "[]"));
        } catch (error) {
            this.bonuses = [];
        }
    }
}
</script>
