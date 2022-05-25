<template>
  <html lang="en">
    <div class="app">
      <div class="content">
        <div class="loading">
          <div v-show="isloading" style="margin-left: 60%; margin-right: 50%; margin-top: 25%;">
            <b-spinner variant="success"></b-spinner>
          </div>
        </div>
        <div class="row">
          <div v-for="graph in graphs" :key="graph.id" class="card col-md-3 mt-3" style="border-radius: 15px">
            <div class="card-body">
              <h5 class="card-title text-success" style="font-weight: 600; font-size: 1.0em;">Grafik {{graph.title}}</h5>
              <img
              :src="graph.image.url"
              :alt="graph.title"
              style="max-width: 100%"
              />
              <p 
              class="card-text" 
              style="color: rgb(65, 65, 65); font-size: 0.8em; text-align: center;"
              >Grafik telah tersedia, silahkan pilih download untuk mengunduh gambar grafik.
              </p>
              <a 
              :href="graph.image.url" 
              target="_blank" 
              class="btn d-flex justify-content-center btn-outline-success" 
              style="border-radius: 20px; font-weight: 600; font-size: 0.9em;"
              >Download
              </a>
            </div>
          </div>
        </div>
      </div>
    <aside class="sidebar" ref="sidebar">
      <div class="form-container">
        <form>
          <div class="title">
            <h4 
            class="text-success" 
            style="font-weight: 600; transition: all 0.5s ease;"
            >Request Grafik
            </h4>
          </div>
          <div class="name">
            <label>Tanggal Mulai</label>
          </div>
            <b-form-datepicker
              format="YYYY-MM-DD"
              v-model="start_date"
              locale="id"
              class="mb-2"
              selected-variant="success"
              placeholder="Pilih Tanggal Mulai"
            ></b-form-datepicker>        
          <div class="name">
            <label>Tanggal Selesai</label>
          </div>
              <b-form-datepicker
                v-model="end_date"
                locale="id"
                class="mb-2"
                selected-variant="success"
                placeholder="Pilih Tanggal Selesai"
              ></b-form-datepicker>
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
          <div class="d-flex flex-row-reverse">
            <b-button
              type="button"
              class="btn shadow-sm p-2"
              variant="success"
              style="font-size: 0.9em; font-weight: 500; transition: all 0.5s ease;"
              @click.prevent="handleOnExecute"
              >Eksekusi</b-button>
          </div>
        </form>
      </div>
    </aside>
      <div class="menu-toggle" id="toggle">
        <button @click="toggleMenu">
          <span>lorem</span>
        </button>
      </div>
    </div>
  </html>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      start_date:'',
      end_date:'',
      graph_choices:[],
      isloading:false, 
      graphs:[],
      isOpen:false,
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
  methods:{
    handleOnExecute(){
    this.toggleIsLoading();
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
        //console.log("response:",response.data);
        this.graphs=response.data.graphs;
      })
      .catch((error) => {
        this.$swal({
        toast: true,
        position: 'top',
        showConfirmButton: false,
        timer: 5000,
        icon: 'error',
        title: 'Oops..',
        text: "Permintaan gagal diproses, silahkan coba kembali!",
      });
        //console.error("error:",error);
      })
      .finally(()=>{
        this.toggleIsLoading();
      });
    },
    toggleIsLoading(){
      this.isloading=!this.isloading;
    },
    date_function: function () {
      var currentDate = new Date();
      console.log(currentDate);

      var formatted_date = new Date().toJSON().slice(0,10).replace(/-/g,'/');
      console.log(formatted_date);
    },
    toggleMenu(){
      console.log("Hello",this.$refs.sidebar);
      if(this.isOpen){
        this.$refs.sidebar.style.display="none";
      }
      else{
        this.$refs.sidebar.style.display="block";
      }
      this.isOpen =!this.isOpen;
    }
    },
    mounted() {
      this.date_function()
    }
}
</script>

<style>
@import "@/css/style.css";
</style>