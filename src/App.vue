<template>
  <v-container>
    <v-card elevation="3">
      <v-card-title>Sistema bancário</v-card-title>

      <v-form class="ma-5 d-flex">
        <v-text-field label="Favorecido" variant="outlined" v-model="conta.favorecido" />
        <v-btn class="mt-2 ms-5" color="green" @click="criarConta">Criar</v-btn>
      </v-form>
    </v-card>
    
    <div class="d-flex justify-center">
      <v-card elevation="3" class="mt-5 mx-3" v-for="conta in listaDeContas" :key="conta.numero">
        <v-flex class="d-flex align-center justify-center">
          <v-card-title class="font-weight-bold">{{ conta.favorecido }}</v-card-title>
          <span>R$ {{ conta.saldo }}</span>
        </v-flex>
        <v-flex class="d-flex flex-column">
          <v-flex class="pa-5">
            <span class="me-5">Agência: {{ conta.agencia }}</span>
            <span>Número: {{ conta.numero }}</span>
          </v-flex>
          <v-flex class="d-flex justify-center ma-5">
            <v-btn color="green" class="me-2" @click="mostrarDialogSacar(conta)">Sacar</v-btn>
            <v-btn color="red" class="me-2" @click="mostrarDialogDepositar(conta)">Depositar</v-btn>
            <v-btn color="blue" @click="mostrarDialogTransferir(conta)">Transferir</v-btn>
          </v-flex>
        </v-flex>
      </v-card>
    </div>
      
    <v-snackbar
      v-model="mostrarSnackbar"
      location="top right"
      close-delay="5000"
    >
      {{ snackbarData.mensagem }}

      <template v-slot:actions>
        <v-btn
          :color="snackbarData.color"
          variant="text"
          @click="mostrarSnackbar = false"
        >
          Fechar
        </v-btn>
      </template>
    </v-snackbar>

    <v-dialog v-model="dialogSacar" width="320px">
      <v-card>
        <v-toolbar color="green">
          <v-card-title>Realizar saque</v-card-title>
        </v-toolbar>

        <v-flex class="ma-5">
          <v-text-field type="number" v-model="valor" variant="outlined" label="Valor do saque" />
        </v-flex>

        <v-card-actions>
          <v-btn variant="text" color="red" @click="dialogSacar = false">Cancelar</v-btn>
          <v-btn variant="text" color="green" @click="sacar">Realizar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogDepositar" width="320px">
      <v-card>
        <v-toolbar color="red">
          <v-card-title>Realizar depósito</v-card-title>
        </v-toolbar>

        <v-flex class="ma-5">
          <v-text-field type="number" v-model="valor" variant="outlined" label="Valor do depósito" />
        </v-flex>

        <v-card-actions>
          <v-btn variant="text" color="red" @click="dialogDepositar = false">Cancelar</v-btn>
          <v-btn variant="text" color="green" @click="depositar">Realizar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="dialogTransferir" width="320px">
      <v-card>
        <v-toolbar color="blue">
          <v-card-title>Realizar transferência</v-card-title>
        </v-toolbar>

        <v-flex class="ma-5">
          <v-text-field type="number" v-model="valor" variant="outlined" label="Valor do depósito" />

          <v-select label="Escolha a conta" variant="outlined" v-model="contaParaTransferencia" :items="listaDeContas" item-title="favorecido" return-object/>
        </v-flex>

        <v-card-actions>
          <v-btn variant="text" color="red" @click="dialogDepositar = false">Cancelar</v-btn>
          <v-btn variant="text" color="green" @click="transferir">Realizar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>
<script>
export default {
  data(){
    return {
      valor: 0,
      contaSelecionada: null,
      contaParaTransferencia: null,

      listaDeContas: [],
      mostrarSnackbar: false,
      dialogSacar: false,
      dialogDepositar: false,
      dialogTransferir: false,
      snackbarData: {
        mensagem: "Conta criada com sucesso",
        color: "green"
      },
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
      var agenciaPadrao = 1234

      this.conta.agencia = agenciaPadrao

      /* 
        REGRA PARA CALCULAR NUMERO ALEATORIO:

        - Usar Math.random() multiplicado pelo (maiorNumero - menorNumero) + menorNumero

      */
      this.conta.numero = Number.parseInt(Math.random() * (9999 - 1111) + 1111)

      this.snackbarData.mensagem = "Conta criada com sucesso"
      this.snackbarData.color = "green"
      this.mostrarSnackbar = true

      this.listaDeContas.push({...this.conta})

      this.conta = {
        favorecido: "",
        agencia: "",
        numero: "",
        saldo: 0
      }
    },

    mostrarDialogSacar(conta){
      this.contaSelecionada = conta
      this.dialogSacar = true
    },
    mostrarDialogDepositar(conta){
      this.contaSelecionada = conta
      this.dialogDepositar = true
    },
    mostrarDialogTransferir(conta){
      this.contaSelecionada = conta
      this.dialogTransferir = true
    },

    sacar(){
      this.mostrarSnackbar = false

      if(this.valor <= 0){
        this.snackbarData.mensagem = "O valor precisa ser maior do que 0!"
        this.snackbarData.color = "red"
        this.mostrarSnackbar = true
        return
      }

      if(Number.parseFloat(this.valor) > Number.parseFloat(this.contaSelecionada.saldo)){
        this.snackbarData.mensagem = "Saldo insuficiente!"
        this.snackbarData.color = "red"
        this.mostrarSnackbar = true
        return
      }

      // realizar o saque
      this.contaSelecionada.saldo = (this.contaSelecionada.saldo - this.valor).toFixed(2)

      this.snackbarData.mensagem = "Saque realizado com sucesso!"
      this.snackbarData.color = "green"
      this.mostrarSnackbar = true
      this.dialogSacar = false

      this.valor = 0
      this.contaSelecionada = null
    },
    depositar(){

      this.mostrarSnackbar = false

      if(this.valor <= 0){
        this.snackbarData.mensagem = "O valor precisa ser maior do que 0!"
        this.snackbarData.color = "red"
        this.mostrarSnackbar = true
        return
      }

      this.contaSelecionada.saldo = (Number.parseFloat(this.contaSelecionada.saldo) + Number.parseFloat(this.valor)).toFixed(2)

      this.snackbarData.mensagem = "Depósito realizado com sucesso!"
      this.snackbarData.color = "green"
      this.mostrarSnackbar = true

      this.valor = 0
      this.dialogDepositar = false
      this.contaSelecionada = null
    },

    transferir(){
      debugger
      this.mostrarSnackbar = false

      if(this.valor <= 0){
        this.snackbarData.mensagem = "O valor precisa ser maior do que 0!"
        this.snackbarData.color = "red"
        this.mostrarSnackbar = true
        return
      }

      if(Number.parseFloat(this.valor) > Number.parseFloat(this.contaSelecionada.saldo)){
        this.snackbarData.mensagem = "Saldo insuficiente!"
        this.snackbarData.color = "red"
        this.mostrarSnackbar = true
        return
      }

      // saco da conta que vai ser retirado
      this.contaSelecionada.saldo = (this.contaSelecionada.saldo - this.valor).toFixed(2)
      // tranfiro o valor
      this.contaParaTransferencia.saldo = (Number.parseFloat(this.contaParaTransferencia.saldo) + Number.parseFloat(this.valor)).toFixed(2)

      this.snackbarData.mensagem = "Transferência realizada com sucesso!"
      this.snackbarData.color = "green"
      this.mostrarSnackbar = true

      this.valor = 0
      this.dialogTransferir = false
      this.contaSelecionada = null
      this.contaParaTransferencia = null
    }
  }
}
</script>
<style>
  
</style>