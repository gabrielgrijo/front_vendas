<template>

  <div>
    <v-toolbar flat>

      <v-toolbar-title>{{ title }}</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-dialog v-model="dialog" max-width="700px">

        <v-btn slot="activator" color="primary" dark flat fab title="Inserir um novo registro"> {{ buttonTitle }}
        </v-btn>

        <v-card>
          <v-card-title>
            <span class="headline">{{ formTitle }}</span>
          </v-card-title>

          <v-card-text>
            <v-container grid-list-xl>
              <v-layout wrap row>
                <v-flex xs12>
                  <v-text-field v-model="cliente.nome" label="Nome"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field v-model="cliente.cpf" label="CPF"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field v-model="cliente.telefone" label="Telefone"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field v-model="cliente.email" label="E-mail"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field v-model="cliente.endereco" label="Endereço"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field v-model="cliente.numero" label="Numero"></v-text-field>
                </v-flex>
                 <v-flex xs12>
                  <v-text-field v-model="cliente.complemento" label="Complemento"></v-text-field>
                </v-flex>
                 <v-flex xs12>
                  <v-text-field v-model="cliente.bairro" label="Bairro"></v-text-field>
                </v-flex>
                 <v-flex xs12>
                  <v-text-field v-model="cliente.cep" label="Cep"></v-text-field>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" flat @click="initialize();">Cancelar</v-btn>
            <v-btn color="blue darken-1" flat @click="save();">Salvar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-toolbar>

    <v-data-table :headers="headers" :items="clientes" hide-actions class="elevation-1">
      <template slot="items" slot-scope="props">
        <td>{{ props.item.nome }}</td>
        <td class="text-xs-center">{{ props.item.email }}</td>
        <td class="text-xs-center">{{ props.item.telefone }}</td>
        <td class="justify-center layout px-0">
           <v-icon small class="mr-2" @click="description(props.item)" title="Descrição">description</v-icon>
          <v-icon small class="mr-2" @click="edit(props.item)" title="Editar registro">edit</v-icon>
          <v-icon small @click="remove(props.item)" title="Excluir registro">delete</v-icon>
        </td>
      </template>

      <template slot="footer" v-if="records > 0">
        <td colspan="100%">
          <strong> {{ footerText }} {{ records }} </strong>
        </td>
      </template>

      <template slot="no-data" v-else>
        <strong> {{ emptyRecordsText }} </strong>
      </template>
    </v-data-table>
  </div>

</template>

<script>
  import ClienteService from '../service/ClienteService';

  export default {
    data: () => ({
      title: "Listagem de Cliente",
      buttonTitle: "Novo",
      dialog: false,
      footerText: "Total de registros: ",
      emptyRecordsText: "Nenhum registro encontrado",
      records: 0,
      clientes: [],
      cliente: {},
      headers: [{
          text: 'Nome',
          align: "center",
          value: 'name'
        },
        {
          text: 'E-mail',
          align: "center",
          value: 'email'
        },
        {
          text: 'Telefone',
          align: "center",
          value: 'telefone'
        },
        { text: 'Ações', value: 'id', sortable: false },
        {},
      ],
    }),

    computed: {
      formTitle() {
        return this.cliente._id ? 'Editar Cliente' : 'Novo Cliente'
      }
    },

    watch: {
      dialog(val) {
        val || this.initialize();
      }
    },

    created() {
      this.initialize();
    },

    methods: {
      calculateRecords() {
        let amount = 0;
        for (let i = 0; i < this.clientes.length; i++) {
          amount++;
        }
        this.records = amount;
      },

      async initialize() {
        this.clientes = await ClienteService.getAll();
        this.cliente = {};
        this.dialog = false;
        this.calculateRecords();
      },

      edit(p) {
        this.cliente = p;
        this.dialog = true;
      },

      async remove(cliente) {
        if (confirm('Tem certeza que deseja excluir este registro ?')) await ClienteService.remove(cliente);
        this.initialize();
      },

      async save() {
        if (this.cliente._id) {
          await ClienteService.update(this.cliente);
          this.initialize();
        } else {
          await ClienteService.save(this.cliente);
          this.initialize();
        }
        this.initialize();
        this.cliente = {};
      }, // save()

      async clear(){
          this.cliente = {};
      }
    }
  }

</script>

<style>

</style>
