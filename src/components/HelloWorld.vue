<template>
  <v-container>
    <v-layout
      text-center
      wrap
    >
      <v-flex xs12>
        <v-img
          :src="require('../assets/logo.svg')"
          class="my-3"
          contain
          height="200"
        ></v-img>
      </v-flex>

      <v-flex md12 mb-4>
        <h1 class="display-2 font-weight-bold mb-3">
          Bem Vindo a Loja de Sistemas
        </h1>
        <p class="subheading font-weight-regular">
          Aplicação criada apenas para demonstração na disciplina de Sistemas Distribuídos,
          <br>Direitos reservados a Manoel Tavares
        </p>
      </v-flex>

      <v-flex md12 mb-4>
        <h1 class="display-2 font-weight-bold mb-3">
          Produtos em Promoção
        </h1>
        <v-container>
          <v-row dense>
            <v-col
              v-for="(item, i) in items"
              :key="i"
              cols="3"
              
            >
              <v-card
                :color="item.color"
                dark
              >
                <div class="d-flex flex-no-wrap justify-space-between">
                  <div>
                    <v-card-title
                      class="headline"
                      v-text="item.title"
                    ></v-card-title>
                    <v-card-text>Preço: R$ {{item.price}}</v-card-text>            
                  </div>

                  <v-avatar
                    class="ma-3"
                    size="125"
                    tile
                  >
                    <v-btn @click="irProduto(item.id,item.price, item.discount)">Comprar</v-btn>
                  </v-avatar>
                </div>
                
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-flex>
      <v-dialog
      v-model="dialog"
      width="500"
    >
          <v-card>
            <v-card-title
              class="headline grey lighten-2"
              primary-title
            >
              Comprar Produto
            </v-card-title>

            <v-card-text>
              Produto: {{ produto.numero }} <br>
              Preço: {{ produto.price }} <br>
              Quantidade: {{ produto.quantidade || 1}} <br>
              Valor Final: {{ produto.valorFinal }} <br>
              <v-combobox
              @change="produto.valorFinal = produto.quantidade * produto.price"
              v-model="produto.quantidade"
              :items="quantidades"
              label="Selecione um valor"
            ></v-combobox>
            <v-combobox
              @change="produto.valorFinal = produto.quantidade * produto.price"
              v-model="usuario"
              :items="users"
              item-value="id"
              label="Selecione um Cliente"
            ></v-combobox>
            </v-card-text>
          
            <v-divider></v-divider>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="primary"
                text
                @click="comprar()"
              >
                Comprar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      
    </v-layout>
  </v-container>
</template>

<script>
import axios from 'axios';
export default {
  name: 'HelloWorld',

  data: () => ({
    dialog: false,
    usuario: null,
    produto: {
      numero: 0,
      price: 0,
      quantidade: 1,
      discount: '',
      valorFinal: 0
    },
    quantidades:[
      1,2,3,4,5
    ],
    userCount: 0,
    users: [],
    items:[
      { id : 1, color: 'red', title: 'Produto', desc: '10% OFF', discount: 10, price: 300},
      { id : 2, color: 'primary', title: 'Produto 01', desc: '20% OFF',discount: 20 , price: 100},
      { id : 3, color: 'dark', title: 'Produto 01', desc: '20% OFF', discount: 20 , price: 250},
      { id : 4, color: 'orange', title: 'Produto 01',desc: '15% OFF' , discount: 15 , price: 85},
      { id : 5, color: 'green', title: 'Produto 01',desc: '10% OFF', discount: 10 , price: 90},
      { id : 6, color: 'purple', title: 'Produto 01',desc: '50% OFF', discount: 50 , price: 42},
      { id : 7, color: 'blue', title: 'Produto 01', desc: '10% OFF',discount: 10 , price: 36},
      { id : 8, color: 'yellow', title: 'Produto 01', desc: '12% OFF' ,discount: 12 , price: 50},
    ]
  }),
  methods: ({
    irProduto(ind,price, discount){
      this.dialog = true;
      this.produto.numero = `Produto ${ind}`;
      this.produto.price = price;
      this.produto.valorFinal = price * this.produto.quantidade;
    },
    getStats(){
      axios.get('http://localhost:3000/stats')
        .then(res => {
          this.userCount = res.data.users,
          this.users = res.data.lastInto.map(item => {
            return {
              text: item.name,
              value: item.id
            }
          })
        })
    },
    comprar(){
      this.dialog = false;
      alert('Compra Realizada com Sucesso');
      axios.put(`http://localhost:3000/users/${this.usuario.value}`,{
        compras: 1,
        pontos: this.produto.price * 5
      })
    }
  }),
  mounted(){
    this.getStats()
  }
};
</script>
