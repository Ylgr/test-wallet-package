<template>
  <div>
    <button v-on:click="logInfo()">print log</button>
    <br/>
    <h1>Current support wallet: {{ availableWallet }}</h1>
    <br/>
    <button v-if="availableWallet && availableWallet.includes('Metamask')"
            v-on:click="DecentralizeWalletManager().connectWallet('Metamask', 1000000)">Connect metamask
    </button>
    <button
            v-on:click="createWallet()">Create wallet
    </button>
    <button v-on:click="importPrivateKey()">Import private key</button>
    <button v-on:click="importSeedPhrase()">Import Mnemonic</button>
    <button v-on:click="importKeystore()">Import Keystore</button>
    <button v-on:click="sendBNB()">Send BNB</button>
    <button v-on:click="sendDFY()">Send DFY</button>
    <br/>
    <button v-on:click="getTransactions()"> Get Tx </button>
  </div>
</template>


<script>
import {DecentralizeWalletManager, TransactionServices} from 'dAppWalletUtils';

export default {
  name: 'Example',
  data: function () {
    return {
      availableWallet: null,
      privateKeyTest: process.env.VUE_APP_PRIVATE_KEY_TEST,
      mnemonicTest: process.env.VUE_APP_MNEMONIC_TEST,
      passwordTest: process.env.VUE_APP_PASSWORD_TEST,
      toAddressTest: process.env.VUE_APP_ADDRESS_TO_TEST,
    }
  },
  mounted() {
    this.availableWallet = DecentralizeWalletManager.checkSupportedWalletsType()
  },
  methods: {
    logInfo() {
      console.log(DecentralizeWalletManager.tokens())
      console.log(DecentralizeWalletManager.currentAddress())
      console.log(DecentralizeWalletManager.currentWalletType())
      console.log(DecentralizeWalletManager.supportedWalletsType())
    },
    DecentralizeWalletManager() {
      return DecentralizeWalletManager
    },
    createWallet() {
      const newWallet = DecentralizeWalletManager.createWallet(this.passwordTest)
      console.log('newWallet: ', newWallet)
    },
    importPrivateKey() {
      console.log('this.privateKeyTest: ', this.privateKeyTest)
      DecentralizeWalletManager.importPrivateKey(this.privateKeyTest, this.passwordTest)
    },
    importSeedPhrase() {
      console.log('this.mnemonicTest: ', this.mnemonicTest)
      DecentralizeWalletManager.importSeedPhrase(this.mnemonicTest, this.passwordTest)
    },
    importKeystore() {
      const keystoreTest = {
        "version": 3,
        "id": "a8fe0d47-bf8c-410c-89c6-1f98a05d96fa",
        "address": "d0c78a8841054738c58d090ffedaed75acd51dbf",
        "crypto": {
          "ciphertext": "024546c11972a28cbf807b03d3363c20868a90863942dc220b2fcb648edff0be",
          "cipherparams": {
            "iv": "43ba97fd366c0fc58430c56a35729a95"
          },
          "cipher": "aes-128-ctr",
          "kdf": "scrypt",
          "kdfparams": {
            "dklen": 32,
            "salt": "bfd0848a42fd1012f38ee36144a39eb71e4ae6f9da8985eb82a10027e1cca047",
            "n": 8192,
            "r": 8,
            "p": 1
          },
          "mac": "a3178c0edf9c13ebda13ebf8a61d8930801a9b0caa442c4bbf23aa61504ade74"
        }
      }
      DecentralizeWalletManager.importKeyStore(keystoreTest)
    },
    async sendBNB() {
      console.log('this.toAddressTest: ', this.toAddressTest)
      const estimateGas = await DecentralizeWalletManager.calculateEstimatedGas(this.toAddressTest, 0.01, 'BNB')
      console.log('estimateGas: ', estimateGas)
      const result = await DecentralizeWalletManager.send(this.passwordTest, this.toAddressTest, 0.01, 'BNB', estimateGas.gasPrice,estimateGas.gasLimit)
      alert(JSON.stringify(result))
    },
    async sendDFY() {
      const estimateGas = await DecentralizeWalletManager.calculateEstimatedGas(this.toAddressTest, 0.01, 'DFY')
      console.log('estimateGas: ', estimateGas)
      const result = await DecentralizeWalletManager.send(this.passwordTest, this.toAddressTest, 0.01, 'DFY', estimateGas.gasPrice,estimateGas.gasLimit)
      alert(JSON.stringify(result))
    },
    async getTransactions() {
      const transactions = await TransactionServices.getTransactions(DecentralizeWalletManager.currentAddress())
      console.log('transactions: ', transactions)
    }
  }
}
</script>