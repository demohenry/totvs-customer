<template>
  <v-container>
    <v-checkbox
    :color="showAllCustomers ? 'green' : 'primary'"
      @click="showAllCustomers = !showAllCustomers"
      label='Mostrar todos'
      >

    <!-- <v-btn
      :color="showAllCustomers ? 'error' : 'primary'"
      @click="showAllCustomers = !showAllCustomers"
    >
      {{
        showAllCustomers
          ? 'Clientes Inadiplentes'
          : 'Mostrar todos'
      }}
    </v-btn> -->

    </v-checkbox>
    <v-card
     class="tableWrapper"
     elevation=15
     color='#f3f3f5'
     >
      <v-text-field
        color=green
        v-model="search"
        append-icon="mdi-magnify"
        label="Procurar"
        single-line
        hide-details
      ></v-text-field>

      <v-data-table
        :headers="headers"
        :items="customers"
        class="elevation-1"
        :search="search"
        :footer-props="{
          'items-per-page-text':'Clientes por página'
        }"
      >
      </v-data-table>
    </v-card>
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue';
import CustomerService from '../services/customer';
import { CustomerType } from '../types/customer.type';

export default Vue.extend({
  name: 'Table',
  data() {
    return {
      showAllCustomers: false,
      allCustomers: [],
      headers: [
        {
          text: 'Nome',
          value: 'name',
        },
        {
          text: 'CPF',
          value: 'cpf',
        },
        {
          text: 'Inadimplente',
          value: 'isDefaulting',
        },
        {
          text: 'Saldo',
          value: 'amount',
        },
        {
          text: 'Vencimento',
          value: 'since',
        },
      ],
      search: '',
    };
  },

  async mounted() {
    const allCustomers: CustomerType[] = await CustomerService.list();

    allCustomers.forEach((customer) => {
      customer.since = customer.since
        ? new Intl.DateTimeFormat('pt-br').format(new Date(customer.since!))
        : '---';

      customer.isDefaulting = customer.isDefaulting ? 'Sim' : 'Não';

      customer.amount = customer.amount
        ? new Intl.NumberFormat('pt-br', {
            style: 'currency',
            currency: 'BRL',
          }).format(+customer.amount)
        : '---';

      (this.allCustomers as CustomerType[]) = allCustomers;
    });
  },


  computed: {
    customers: function(): CustomerType[] {
      if (this.showAllCustomers) {
        return this.allCustomers;
      } else {
        const customers: CustomerType[] = [];
        this.allCustomers.forEach((customer: CustomerType) => {
          if (customer.isDefaulting !== 'Não') {
            customers.push(customer);
          }
        });
        return customers;
      }
    },
  },
});
</script>

<style lang="scss">
.v-input {
  position: relative;
  top: 10px;
  right: 19rem;

  width: 30rem;
  margin: 0 auto 2rem
}

v-checkbox {
 position: absolute;
 right: 100px;
}

/* .v-btn {
  margin-bottom: 1rem;
} */



</style>
