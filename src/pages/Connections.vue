<template>
  <q-page class="container">
    <q-dialog v-model="dialog" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">Código de Verificación</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <!-- {{ dateNow }}Q -->
          <q-input dense v-model="verification" autofocus />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Cancelar" v-close-popup @click="router.push('/')" />
          <q-btn flat label="Verificar" v-close-popup @click="verificarToken" />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <div class="row justify-center q-mt-lg">
      <q-table
        title="Conexiones"
        :data="items"
        :columns="columns"
        row-key="name">
        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td key="id" :props="props">
              {{ props.row.id }}
            </q-td>
            <q-td key="ip" :props="props">
              {{ props.row.ip }}
            </q-td>
            <q-td key="mac" :props="props">
              {{ props.row.mac }}
            </q-td>
            <q-td key="fec_conection" :props="props">
              {{ props.row.fec_conection }}
            </q-td>
            <q-td key="time" :props="props">
              <!-- <q-badge color="green"> -->
                {{ props.row.time_id }}
              <!-- </q-badge> -->
            </q-td>
            <q-td key="is_active" :props="props">
              <!-- <q-badge color="green"> -->
                {{ props.row.is_active ? 'Activo' : 'Desconectado' }}
              <!-- </q-badge> -->
            </q-td>
            <q-td key="actions" :props="props">
                <!-- <q-btn class="q-ml-xs" @click="edit(props.row)" color="info" round icon="edit" size="sm" /> -->
                <!-- <q-btn class="q-ml-xs" @click="showQrModal(props.row)" color="info" round icon="qr_code_2" size="sm" /> -->
                <q-btn class="q-ml-xs" @click="deleteR(props.row)" color="red" round icon="delete" size="sm" />
            </q-td>
            </q-tr>
          </template>
      </q-table>
    </div>

    <!-- Editar -->
    <q-dialog v-if="show" v-model="show" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">Editar Tiempo</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <div class="col-12 q-pt-lg text-center text-blue-grey-9">
            <span class="text-subtitle1">
              Introduzca el Tiempo en minutos
            </span>
          </div>
          <div class="col-10 q-pt-lg">
            <q-input rounded outlined v-model="form.time" label="Tiempo" />
          </div>
          <!-- <div class="col-12 q-pt-md text-center">
            <qriously :value="text" :size="230" />
          </div> -->
          <!-- <q-btn
            color="blue-grey-10"
            outline
            label="Descargar"
            @click="donwloadCanvas"
          /> -->
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Cancelar" v-close-popup @click="show =  false" />
          <q-btn flat label="Guardar" v-close-popup @click="put" />
        </q-card-actions>
      </q-card>
    </q-dialog>
    <!-- Fin de Editar -->

    <!-- Mostrar QR -->
    <q-dialog v-if="showQr" v-model="showQr" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">QR</div>
        </q-card-section>

        <q-card-section class="q-pt-none text-center">
          <div class="col-12 q-pt-md text-center">
            <qriously :value="item.id.toString()" :size="230" />
          </div>
          <q-btn
            color="blue-grey-10"
            outline
            label="Descargar"
            @click="donwloadCanvas"
          />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Cerrar" v-close-popup @click="()=> {item='';showQr=false}" />
          <!-- <q-btn flat label="Verificar" v-close-popup @click="verificarToken" /> -->
        </q-card-actions>
      </q-card>
    </q-dialog>
    <!-- Fin de Mostrar qr -->
  </q-page>
</template>

<script>
import dayjs from 'dayjs'
import axios from 'axios'
import { API, TOKEN } from './../constants'
export default {
  name: 'Connections',
  computed: {
    dateNow () {
      return dayjs().format('DD/MM/YYYY HH:mm:ss')
    }
  },
  mounted () {
    this.getTimes()
    this.tokenVencido()
    // eslint-disable-next-line space-in-parens
    this.interval = setInterval( () => {
      if (this.tokenVencido()) {
        this.$router.push('/')
      }
    }, 50000)
  },
  unmounted () {
    clearInterval(this.interval)
  },
  data () {
    return {
      interval: '',
      verification: '',
      dialog: false,
      showQr: false,
      show: false,
      createModal: false,
      items: [],
      item: '',
      form: {},
      columns: [
        {
          name: 'id',
          required: true,
          label: 'Id',
          align: 'center',
          field: row => row.id,
          format: val => `${val}`,
          sortable: true
        },
        {
          name: 'ip',
          required: true,
          label: 'IP',
          align: 'center',
          field: row => row.ip,
          format: val => `${val}`,
          sortable: true
        },
        {
          name: 'mac',
          required: true,
          label: 'MAC',
          align: 'center',
          field: row => row.mac,
          format: val => `${val}`,
          sortable: true
        },
        {
          name: 'fec_conection',
          required: true,
          label: 'Fecha Conexión',
          align: 'center',
          field: row => row.fec_conection,
          format: val => `${val}`,
          sortable: true
        },
        {
          name: 'time',
          required: true,
          label: 'Tiempo',
          align: 'center',
          field: row => row.time_id,
          format: val => `${val}`,
          sortable: true
        },
        {
          name: 'is_active',
          required: true,
          label: 'Status',
          align: 'center',
          field: row => row.is_active,
          format: val => `${val}`,
          sortable: true
        },
        {
          name: 'actions',
          required: false,
          label: 'Acciones',
          align: 'center',
          field: row => row.id,
          format: val => `${val}`,
          sortable: false
        }
      ],
      text: 'https://github.com/patrickmonteiro'
    }
  },
  methods: {
    openCreateModal () {
      this.createModal = true
      this.form = {
        time: ''
      }
    },
    async storeTime () {
      const url = API + 'conection'
      const data = this.form
      await axios.post(url, { data })
      this.form = ''
      this.getTimes()
    },
    edit (element) {
      // console.log(element)
      this.item = element
      this.form.time = element.time
      this.show = true
    },
    async put () {
      const url = API + 'conection/' + this.item.id
      const data = {
        time: this.item.time
      }
      await axios.put(url, { data })
      this.item = ''
      this.getTimes()
    },
    showQrModal (element) {
      this.item = element
      // console.log(this.item.time)
      this.showQr = true
    },
    async deleteR (element) {
      const url = API + 'conection/' + element.id
      await axios.delete(url)
      this.getTimes()
    },
    async getTimes () {
      const url = API + 'conection'
      const res = await axios.get(url)
      this.items = res.data
    },
    donwloadCanvas () {
      const canvas = document.getElementsByTagName('canvas')
      const link = document.createElement('a')
      link.download = 'QRCode.png'
      link.href = canvas[0].toDataURL()
      link.click()
    },
    tokenVencido () {
      const permiss = localStorage.getItem('permiss')
      if (!permiss) {
        this.dialog = true
        return true
      }
      const permissDate = dayjs(permiss)
      if (dayjs().subtract(10, 'min') >= permissDate) {
        this.dialog = true
        return true
      }
      return false
    },
    verificarToken () {
      if (this.verification === TOKEN) {
        localStorage.setItem('permiss', dayjs().format('DD/MM/YYYY HH:mm:ss'))
      }
      this.dialog = false
    }
  }
}
</script>
