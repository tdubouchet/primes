<template>
    <v-dialog v-model="dialog" max-width="390">
        <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" dark v-bind="attrs" v-on="on">
                <v-icon left>mdi-plus</v-icon> Ajouter une prime
            </v-btn>
        </template>
        <v-card>
            <v-form @submit.prevent="addBonus">
                <v-card-title>
                    Nouvelle prime 😎
                </v-card-title>

                <v-divider></v-divider>

                <v-card-text>
                    <v-text-field
                        placeholder="chiffre d'affaire généré"
                        prefix="Prime sur"
                        solo
                        autocomplete="off"
                        spellcheck="false"
                        v-model="bonus.name"
                        dense
                    ></v-text-field>

                    <v-select
                        v-model="bonus.type"
                        :menu-props="{ offsetY: true }"
                        prefix="En"
                        dense
                        :items="bonusTypes"
                        item-text="name"
                        item-value="value"
                        solo
                    ></v-select>

                    <v-text-field
                        v-model="bonus.bonusValue"
                        prefix="Prime : "
                        :suffix="symbol"
                        outlined autocomplete="off"
                        spellcheck="false"
                        dense
                        placeholder="0"
                        type="number"
                        :min="0"
                    ></v-text-field>

                    <v-text-field
                        prefix="Valeur max : "
                        outlined
                        autocomplete="off"
                        spellcheck="false"
                        v-model="bonus.maxValue"
                        dense
                        placeholder="0"
                        type="number"
                        :min="0">
                    </v-text-field>

                    <v-text-field
                        prefix="Valeur par défaut : "
                        outlined
                        autocomplete="off"
                        spellcheck="false"
                        v-model="bonus.value"
                        dense
                        placeholder="0"
                        type="number"
                        :min="0">
                    </v-text-field>

                    <v-btn block color="primary" type="submit">
                        <v-icon left small>mdi-plus</v-icon> Créer
                    </v-btn>
                </v-card-text>
            </v-form>
        </v-card>
    </v-dialog>
</template>

<script>
    const defaultBonus = () => ({
        name            : '',
        type            : 'percentage',
        value           : 0,
        bonusValue      : 0,
        maxValue        : 0
    });

    export default {
        name : 'create-bonus-modal',

        data : () => {
            return {
                dialog  : false,
                bonus   : defaultBonus(),
                bonusTypes      : [
                    {
                        name    : 'pourcentage',
                        value   : 'percentage'
                    },
                    {
                        name    : 'euro',
                        value   : 'euro'
                    },
                ]
            }
        },

        computed : {
            symbol() {
                return this.bonus.type === 'euro'
                    ? '€'
                    : '%';
            }
        },

        methods : {
            addBonus() {
                this.$emit('addBonus', this.bonus);

                this.bonus  = defaultBonus();
                this.dialog = false;
            },
        }
    }
</script>
