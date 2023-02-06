<template>
  <v-container>
    <v-card elevation="3">
      <v-card-title>Carro Ferrari</v-card-title>
      <v-card-item >
        <v-img class="rounded-lg" src="https://s2.glbimg.com/6N6UKj1cx5Nv6hZf3BmiHwx7Y1A=/0x0:1080x645/924x0/smart/filters:strip_icc()/i.s3.glbimg.com/v1/AUTH_cf9d035bf26b4646b105bd958f32089d/internal_photos/bs/2022/Z/F/C923T1TlORxpzmAb8lAg/ferraro.jpg" width="240px"></v-img>
      </v-card-item>

      <v-form class="ma-5 d-flex">
        <v-text-field label="Favorecido" variant="outlined" v-model="conta.favorecido" />
        <v-btn class="mt-2 ms-5" color="green" @click="criarConta">Criar</v-btn>
      </v-form>
    </v-card>
    
    <div class="d-flex justify-center">

      <v-card elevation="3" class="mt-5 mx-3" v-for="conta in listaDeContas" :key="conta.numero">
        <v-card-title class="font-weight-bold">{{ conta.favorecido }} <v-spacer /> R$ {{ conta.saldo }}</v-card-title>
      </v-card>
    </div>
      
    <v-snackbar
      v-model="mostrarSnackbar"
      location="top right"
    >
      Conta criada com sucesso

      <template v-slot:actions>
        <v-btn
          color="pink"
          variant="text"
          @click="mostrarSnackbar = false"
        >
          Fechar
        </v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>
<script>
export default {
  data(){
    return {
      listaDeContas: [],
      mostrarSnackbar: false,
      conta: {
        favorecido: "",
        agencia: "",
        numero: "",
        saldo: 0
      }
    }
  },
  methods: {
    criarConta(){
      debugger
      var agenciaPadrao = 1234

      this.conta.agencia = agenciaPadrao
      this.conta.numero = Number.parseInt(Math.random() * (9999 - 1111) + 1111)

      this.mostrarSnackbar = true

      this.listaDeContas.push({...this.conta})
    }
  }
}
</script>
<style>
  
</style>