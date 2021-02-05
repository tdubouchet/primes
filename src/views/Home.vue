<template>
    <v-card max-width="800" class="mx-auto" elevation="0">
        <v-toolbar flat dense>
            <v-toolbar-title>
                <span class="subheading">Calculateur de prime 🚀</span>
            </v-toolbar-title>

            <v-spacer></v-spacer>

            <CreateBonusModal v-on:addBonus="addBonus"></CreateBonusModal>
        </v-toolbar>

        <v-card-text>
            <v-row>
                <v-col cols="12" class="text-center">
                    <v-chip x-large label v-if="bonuses.length">
                        <span>Prime : <b class="primary--text">{{ totalBonus | currency }}</b> brut 💰</span>
                    </v-chip>

                    <h4 v-else>
                        Commencez par ajouter une prime
                    </h4>
                </v-col>

                <v-col>
                    <v-card class="mt-3" v-for="(bonus, index) in bonuses" :key="`bonus_${index}`">
                        <v-toolbar flat dense>
                            <v-toolbar-title>
                                <span class="subheading text-uppercase">{{ bonus.name }}</span>
                            </v-toolbar-title>
                            <v-spacer></v-spacer>

                            <v-btn dark icon>
                                <v-icon>mdi-dots-vertical</v-icon>
                            </v-btn>

                            <v-btn icon color="red" @click="deleteBonus(index)">
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                        </v-toolbar>

                        <v-card-text>
                            <v-row class="mb-4" justify="space-between">
                                <v-col class="text-left">
                                    <span class="display-3 font-weight-light">
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

                            <v-row>
                                <v-col>
                                    <v-text-field dense :label="bonus.name" hide-details v-model="bonus.value" outlined type="number" :max="bonus.maxValue"></v-text-field>
                                </v-col>
                                <v-col>
                                    <v-text-field dense label="Valeur maximum" hide-details v-model="bonus.maxValue" outlined type="number"></v-text-field>
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
