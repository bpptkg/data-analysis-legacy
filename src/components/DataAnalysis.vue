<template>
  <html lang="en">
    <div class="app">
      <div class="content">
        <div class="loading graph-body">
          <div v-show="is_loading" style="margin: 50%; margin-top: 30%;">
            <b-spinner variant="success"></b-spinner>
          </div>
        </div>
        <div class="row graph-body" style="padding: 10px;">
          <div v-for="graph in graphs" :key="graph.id" class="card col-md-4 mt-3" style="border-radius: 12px; border-color: transparent;">
            <div class="card-body">
              <h5 
                class="card-title text-success" 
                style="font-weight: 600; font-size: 1.0em;"
                >Grafik {{graph.title}}
              </h5>
              <img
                :src="graph.image.url"
                :alt="graph.title"
                style="max-width: 100%"
              />
              <a 
                class="btn d-flex justify-content-center btn-outline-success" 
                :href="graph.image.url" 
                target="_blank" 
                style="border-radius: 10px; font-weight: 600; font-size: 0.9em;"
                >Download
              </a>
            </div>
          </div>
        </div>
      </div>
  <transition name="slide">
    <aside v-if="show" class="sidebar">
      <div class="form-container">
        <form>
          <div class="title">
            <h4 
              class="text-success" 
              style="font-weight: 600;"
              >Request Grafik
            </h4>
          </div>
          <div class="name">
            <label>Tanggal Mulai</label>
          </div>
            <b-form-datepicker
              class="mb-2"
              v-model="start_date"
              locale="id"
              selected-variant="success">
            </b-form-datepicker>        
          <div class="name">
            <label>Tanggal Selesai</label>
          </div>
            <b-form-datepicker
              class="mb-2"
              v-model="end_date"
              locale="id"
              selected-variant="success">
            </b-form-datepicker>
          <div class="name">
            <label>Pilih Grafik</label>
          </div>
            <b-dropdown class="w-100" text="Pilih Grafik" variant="outline-success">
              <div class="choices d-flex flex-column">
                <label class="col" v-for="choice in options" :key="choice.name">
                  <input 
                    v-model="graph_choices"
                    type="checkbox"
                    :value="choice.name"
                  />
                  {{choice.title}}
                </label>
              </div>
            </b-dropdown>
          <br><br><br>
          <div class="execute d-flex flex-row-reverse">
            <b-button
              class="button shadow-sm p-2"
              type="button"
              variant="success"
              @click.prevent="handleOnExecute"
              >Eksekusi</b-button>
          </div>
        </form>
      </div>
    </aside>
  </transition>
      <div class="menu-toggle">
        <b-button 
          class="menu-toggle shadow-sm"
          type="button" 
          variant="success"
          style="border-radius: 100px; transition: all 0.5s ease;"
          v-on:click="show=!show"
          >
          <div class="h7">
            <b-icon-grid-fill></b-icon-grid-fill>
          </div>
        </b-button>
      </div>
    </div>
  </html>
</template>

<script>
import axios from 'axios'
import moment from 'moment'

export default {
  data() {
    return {
      start_date: moment().subtract(7,'days').format("YYYY-MM-DD"),
      end_date: moment().format("YYYY-MM-DD"),
      graph_choices: [],
      is_loading: false, 
      graphs: [],
      show: true,
      options: [
        {
          name: "doas",
          title: "DOAS",
        },
        {
          name: "awanpanas",
          title: "Jarak Luncur AP",
        },
        {
          name: "edm",
          title: "EDM",
        },
        {
          name: "energy",
          title: "Energi Seismik",
        },
        {
          name: "gps_bpptkg",
          title: "GPS BPPTKG",
        },
        {
          name: "gps_dels",
          title: "GPS Deles",
        },
        {
          name: "gps_klat",
          title: "GPS Klatakan",
        },
        {
          name: "gps_pasb",
          title: "GPS Pasarbubar",
        },
        {
          name: "seismicity",
          title: "Seismisitas",
        },
        {
          name: "rsam_infrasound_kendit",
          title: "RSAM Infrasound Kendit",
        },
        {
          name: "rsam_seismic_kendit",
          title: "RSAM Seismik Kendit",
        },
        {
          name: "rsam_seismic_pasarbubar",
          title: "RSAM Seismik Pasarbubar",
        },
        {
          name: "thermal",
          title: "Thermal",
        },
        {
          name: "joinplot",
          title: "Multi-Parameter",
        },
        {
          name: "joinplot_phases",
          title: "Multi-Parameter dengan Fase",
        },
        {
          name: "tiltborehole_bawahkendit",
          title: "Tiltmeter Borehole Bawah Kendit",
        },
        {
          name: "tiltborehole_kendit",
          title: "Tiltmeter Borehole Kendit",
        },
        {
          name: "tiltborehole_klatakan",
          title: "Tiltmeter Borehole Klatakan",
        },
        {
          name: "tiltborehole_lavaflow1902",
          title: "Tiltmeter Borehole Lava Flow 1902",
        },
        {
          name: "tiltborehole_lavaflow1953",
          title: "Tiltmeter Borehole Lava Flow 1953",
        },
        {
          name: "tiltmeter_babadan",
          title: "Tiltmeter Babadan",
        },
        {
          name: "tiltmeter_grawah",
          title: "Tiltmeter Grawah",
        },
        {
          name: "tiltmeter_gunungijo",
          title: "Tiltmeter Gunungijo",
        },
        {
          name: "tiltmeter_klatakan",
          title: "Tiltmeter Klatakan",
        },
        {
          name: "tiltmeter_labuhan",
          title: "Tiltmeter Labuhan",
        },
        {
          name: "tiltmeter_patuk",
          title: "Tiltmeter Patuk",
        },
        {
          name: "tiltmeter_selokopo",
          title: "Tiltmeter Selokopo",
        },
        {
          name: "weekly_report",
          title: "Grafik Laporan Mingguan",
        },
        {
          name: "vogamos",
          title: "Vogamos",
        },
      ],
    };
  },
methods: {
    handleOnExecute() {
    this.toggleIs_Loading();
    axios
      .post(
        "https://plotrequest.cendana15.com/api/request/",
        {
          start_date: this.start_date,
          end_date: this.end_date,
          graph_choices: this.graph_choices,
        },
        {
          headers: {
            Authorization: `Api-Key ${process.env.VUE_APP_API_KEY}`,
          },
        }
      )
      .then((response) => {
        this.graphs=response.data.graphs;
      })
      .catch((error) => {
        this.$swal({
        toast: true,
        position: 'top-end',
        showConfirmButton: false,
        timer: 5000,
        timerProgressBar: true,
        icon: 'error',
        title: 'Permintaan gagal diproses, silahkan coba kembali!',
      });
      })
      .finally(() => {
        this.toggleIs_Loading();
      });
    },
    toggleIs_Loading() {
      this.is_loading=!this.is_loading;
    }
  }
}
</script>

<style>
@import "@/css/style.css";
</style>