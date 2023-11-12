<template>
  <q-page>
    <div class="row">
      <div
        v-if="!showCamera"
        class="col-12 text-center q-pt-md"
      >
          <img
          alt="Quasar logo"
          src="/qr_code.svg"
          style="width: 340px"
        >
      </div>
    </div>
    <div class="row justify-center q-pt-lg">
       <div class="col-12 text-center">
        <span class="text-subtitle2 text-grey-9">
          {{ textInfo }}
        </span>
        <q-btn color="secondary" rounded icon="camera_alt" label="Leer Código QR"
          class="full-width" size="lg" @click="turnCameraOn()"
          v-show="!showCamera"/>

          <p class="text-subtitle1" v-if="result">Última lectura: <b>{{ result }}</b></p>
          <div v-if="showCamera">
            <qrcode-stream :camera="camera" @decode="onDecode">
            </qrcode-stream>
          </div>
      </div>
    </div>
  </q-page>
</template>

<style>
</style>

<script>
import axios from 'axios'
import { API } from './../constants'
import { QrcodeStream } from 'vue-qrcode-reader'
export default {
  name: 'PageIndex',
  components: { QrcodeStream },
  data () {
    return {
      isValid: undefined,
      camera: 'auto',
      result: null,
      showCamera: false
    }
  },
  computed: {
    textInfo () {
      return this.showCamera ? 'Posicione el código QR en la cámara' : 'Presione el botón y escaneé el código QR.'
    }
  },
  methods: {
    async onDecode (content) {
      const ip = ''
      const mac = ''
      const data = {
        id: 0,
        ip: ip,
        mac: mac,
        time_id: content,
        is_active: true,
        fec_conection: new Date()
      }
      const url = API + 'conection/'
      try {
        await axios.post(url, data)
        this.result = 'Conectado exitosamente'
      } catch (e) {
        this.result = 'Ha ocurrido un problema al intentar conectar'
      }
      this.turnCameraOff()
    },

    turnCameraOn () {
      this.camera = 'auto'
      this.showCamera = true
    },

    turnCameraOff () {
      this.camera = 'off'
      this.showCamera = false
    }
  }
}
</script>

<style scoped>
/* .validation-success,
.validation-failure,
.validation-pending {
  position: absolute;
  width: 100%;
  height: 100%;

  background-color: rgba(255, 255, 255, .8);
  text-align: center;
  font-weight: bold;
  font-size: 1.4rem;
  padding: 10px;

  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
}
.validation-success {
  color: green;
}
.validation-failure {
  color: red;
} */
</style>
