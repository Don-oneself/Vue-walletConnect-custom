<template>
  <div style="height: 100vh">
    <div @click="openQrCode">QrCode</div>
    <div @click="sendTransaction">Send Transaction</div>
    <div @click="clear">Clear LocalStorage</div>
  </div>
</template>

<script>
// ---- Error ----
// ReferenceError: Buffer is not defined
// https://stackoverflow.com/a/71953677
import { Buffer } from 'buffer'
window.Buffer = Buffer
// ---- End ----

import WalletConnect from "@walletconnect/client";
import QRCodeModal from "@walletconnect/qrcode-modal";

export default {
  name: 'App',
  data() {
    return {
      address: '',
      connector: null
    }
  },
  components: {

  },
  unmounted() {
    this.address = ''
    this.connector = null
  },
  mounted() {
    // Create a connector
    this.connector = new WalletConnect({
      bridge: "https://bridge.walletconnect.org", // Required
      qrcodeModal: QRCodeModal
    });

    // Events: connect, disconnect, session_request, session_update, call_request, wc_sessionRequest, wc_sessionUpdate
    // Subscribe to connection events
    this.connector.on("connect", (error, payload) => {
      if (error) {
        throw error;
      }

      // Get provided accounts and chainId
      console.log('connect')
      console.log(payload)
      // this.address = ...

      // Tip localstorage will add walletconnect
    });

    this.connector.on("session_update", (error, payload) => {
      if (error) {
        throw error;
      }

      // Get updated accounts and chainId
      console.log('session_update')
      console.log(payload)
    });

    this.connector.on("call_request", (error, payload) => {
      if (error) {
        throw error;
      }

      // Get updated accounts and chainId
      console.log('call_request')
      console.log(payload)
    });

    this.connector.on("disconnect", (error, payload) => {
      if (error) {
        throw error;
      }

      // Delete connector
      console.log('Delete connector')
      console.log(payload)
    });
  },
  methods: {
    openQrCode() {
      // Check if connection is already established
      if (!this.connector.connected) {
        // create new session
        this.connector.createSession({chainId: 56});
      }
    },
    // send Transaction
    // The rest of the methods are not supported
    sendTransaction() {
      // Two custom properties
      let obj = {
        currency: 'trx', // busd bnb trx usdt-trc20 Choose one of four - Required
        amount: 100 // Transaction Quantity - Decimal - Required
      }
      const tx = {
        // from: this.address, // Required
        from: '0xbc28Ea04101F03aA7a94C1379bc3AB32E65e62d3', // Required
        to: "TWftpv5uM1nhEfJmn14mCDYYppdzEJbEaa", // Required
        // TODO:: In theory, it should sign, temporarily pass a string first
        data: JSON.stringify(obj)
      };
      this.connector
          .sendTransaction(tx)
          .then((result) => {
            // Returns transaction id (hash)
            console.log(result);
          })
          .catch((error) => {
            // Error returned when rejected
            console.error(error);
          });
    },

    // clear local storage
    clear() {
      window.localStorage.removeItem('walletconnect')
      window.location.reload(true)
    }
  }
}
</script>

<style scoped>
div {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  font-size: 30px;
  flex-direction: column;
  cursor: pointer;
}
</style>
