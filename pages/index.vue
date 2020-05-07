<template>
  <div>
    <v-toolbar dense class="mt-2 mb-4 hidden-lg-and-up">
      <v-toolbar-title>
        <span>{{ stepTitle }}</span>
      </v-toolbar-title>
    </v-toolbar>
    <v-stepper v-model="e1">
      <v-stepper-header>
        <v-stepper-step :complete="e1 > 1" step="1"
          >Seleziona ordine cliente</v-stepper-step
        >

        <v-divider></v-divider>

        <v-stepper-step :complete="e1 > 2" step="2"
          >Aggiungi articoli [{{ ordine && ordine.code }}]</v-stepper-step
        >

        <v-divider></v-divider>

        <v-stepper-step step="3">Riepilogo</v-stepper-step>
      </v-stepper-header>

      <v-stepper-items>
        <v-stepper-content step="1">
          <v-container grid-list-md class="pa-1 px-1">
            <v-card elevation="0">
              <v-form>
                <v-card-text>
                  <v-layout wrap>
                    <v-flex xs12>
                      <v-autocomplete
                        ref="ordine"
                        v-model="ordine"
                        :items="ordini"
                        hide-selected
                        item-text="code"
                        item-value="code"
                        label="Ordine cliente"
                        placeholder="Inzia a digitare per cercare ordine"
                        return-object
                        clearable
                        @change="resetItems"
                        @focus="$event.target.select()"
                      ></v-autocomplete>
                    </v-flex>
                  </v-layout>
                </v-card-text>
              </v-form>
            </v-card>
          </v-container>
        </v-stepper-content>

        <v-stepper-content step="2">
          <v-container grid-list-md class="pa-1 px-1">
            <v-card elevation="0">
              <v-card-text>
                <v-row>
                  <v-col cols="2" md="1" xs="2" sm="1">
                    <v-text-field
                      label="#"
                      :value="itemsCounter"
                      disabled
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4">
                    <v-text-field
                      v-model="item"
                      :counter="15"
                      maxlength="15"
                      label="Barcode Articolo"
                      @keydown.enter.prevent="addItem"
                    ></v-text-field>
                  </v-col>

                  <v-col cols="6" md="1" xs="2" sm="1">
                    <v-text-field
                      v-model="quantity"
                      label="Quantità"
                      type="number"
                      required
                    ></v-text-field>
                  </v-col>

                  <v-col cols="12" md="12" sm="12" xs="12">
                    <v-btn
                      style="width:100%"
                      color="teal"
                      class="mt-3"
                      :disabled="!(item && quantity)"
                      @click="addItem"
                      ><v-icon left>add</v-icon> Aggiungi</v-btn
                    >
                  </v-col>
                </v-row>
                <v-expansion-panels class="mt-5">
                  <v-expansion-panel>
                    <v-expansion-panel-header>{{
                      tableTitle
                    }}</v-expansion-panel-header>
                    <v-expansion-panel-content>
                      <v-data-table
                        dense
                        :headers="headers"
                        :items="items"
                        disable-pagination
                        hide-default-footer
                        class="elevation-0 mt-10"
                      >
                        <template v-slot:item.row="{ item }">
                          {{ itemCounter(item) }}
                        </template>
                        <template v-slot:item.actions="{ item }">
                          <v-icon small @click="removeItem(item)">
                            mdi-delete
                          </v-icon>
                        </template>
                        <template v-slot:no-data>
                          Aggiungi il primo aritcolo
                        </template>
                      </v-data-table>
                    </v-expansion-panel-content>
                  </v-expansion-panel>
                </v-expansion-panels>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn
                  :disabled="items.length === 0"
                  color="primary"
                  @click="e1 = 3"
                  >AVANTI</v-btn
                >
                <v-btn color="secondary" @click="e1 = 1">INDIETRO</v-btn>
              </v-card-actions>
            </v-card>
          </v-container>
        </v-stepper-content>

        <v-stepper-content step="3">
          <v-container>
            <v-flex xs-12>
              <v-card elevation="0">
                <v-card-title>Ordine cliente</v-card-title>
                <v-card-text>
                  <p>{{ ordine && ordine.code }}</p>
                  <v-expansion-panels>
                    <v-expansion-panel>
                      <v-expansion-panel-header
                        >Dettaglio</v-expansion-panel-header
                      >
                      <v-expansion-panel-content>
                        <strong>Data: </strong>
                        <p>{{ ordine && ordine.details.date }}</p>
                        <strong>Richiesta arrivata da: </strong>
                        <p>{{ ordine && ordine.details.orderType }}</p>
                      </v-expansion-panel-content>
                    </v-expansion-panel>
                    <v-expansion-panel>
                      <v-expansion-panel-header>{{
                        recapTitle
                      }}</v-expansion-panel-header>
                      <v-expansion-panel-content>
                        <v-simple-table dense>
                          <template v-slot:default>
                            <thead>
                              <tr>
                                <th class="text-left">Articolo</th>
                                <th class="text-right">Quantità</th>
                              </tr>
                            </thead>
                            <tbody>
                              <tr
                                v-for="article in items"
                                :key="article.barcode"
                              >
                                <td>{{ article.barcode }}</td>
                                <td class="text-right">
                                  {{ article.quantity }}
                                </td>
                              </tr>
                            </tbody>
                          </template>
                        </v-simple-table>
                      </v-expansion-panel-content>
                    </v-expansion-panel>
                  </v-expansion-panels>
                </v-card-text>
                <v-card-actions>
                  <v-spacer />
                  <v-btn color="primary" @click="submit">Invia</v-btn>
                  <v-btn color="secondary" @click="e1 = 2">INDIETRO</v-btn>
                </v-card-actions>
              </v-card>
            </v-flex>
          </v-container>
        </v-stepper-content>
      </v-stepper-items>
      <v-snackbar v-model="snackbar" color="success" :timeout="timeout" top>
        Inviato con successo
      </v-snackbar>
    </v-stepper>
  </div>
</template>

<script>
export default {
  data() {
    return {
      timeout: 3000,
      snackbar: false,
      e1: 1,
      headers: [
        {
          text: '',
          value: 'actions',
          sortable: false,
          align: 'left',
          width: '5%'
        },
        {
          text: '#',
          value: 'row',
          align: 'left',
          sortable: true,
          width: '10%'
        },
        {
          text: 'Articolo',
          value: 'barcode'
        },
        {
          text: 'Quantità',
          value: 'quantity',
          align: 'left',
          sortable: false
        }
      ],
      ordini: [
        {
          code: 'OC001 - Mario Rossi',
          details: {
            date: '07/05/2020',
            orderType: 'EMail'
          }
        },
        {
          code: 'OC002 - Luca Bianchi',
          details: {
            date: '05/05/2020',
            orderType: 'Telefono'
          }
        }
      ],
      ordine: null,
      item: null,
      items: [],
      quantity: 1
    }
  },
  computed: {
    tableTitle() {
      return `${
        this.items.length === 1
          ? `${this.items.length} Articolo aggiunto`
          : `${this.items.length} Articoli aggiunti`
      } `
    },
    recapTitle() {
      return `${
        this.items.length === 1
          ? `${this.items.length} Articolo`
          : `${this.items.length} Articoli`
      } `
    },
    stepTitle() {
      if (this.e1 === 1) {
        return 'Seleziona ordine'
      } else if (this.e1 === 2) {
        return `Aggiungi articoli [${this.ordine && this.ordine.code}]`
      } else {
        return 'Riepilogo'
      }
    },
    itemsCounter() {
      return this.items.length + 1
    }
  },
  mounted() {
    this.$refs.ordine.$el.focus()
  },
  methods: {
    addItem() {
      if (this.item) {
        const index = this.items.findIndex((x) => x.barcode === this.item)
        if (index >= 0) {
          this.items[index].quantity += parseFloat(this.quantity)
        } else {
          const obj = {
            barcode: this.item,
            quantity: parseFloat(this.quantity)
          }
          this.items.push(obj)
        }

        this.item = null
        this.quantity = 1
      }
    },
    resetItems() {
      this.items = []
      if (this.ordine) {
        this.e1 = 2
      }
    },
    removeItem(item) {
      this.items = this.items.filter((x) => x.barcode !== item.barcode)
    },
    submit() {
      this.e1 = 1
      this.snackbar = true
      this.items = []
      this.ordine = false
      this.item = null
      this.quantity = 1
    },
    itemCounter(item) {
      return (
        this.items
          .map(function(x) {
            return x.barcode
          })
          .indexOf(item.barcode) + 1
      )
    }
  }
}
</script>
