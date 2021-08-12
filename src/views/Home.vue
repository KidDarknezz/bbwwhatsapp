<template>
  <q-page class="bg-grey-2">
    <div
      class="gingham-banner q-mb-lg row items-center justify-center shadow-5"
    >
      <BBWTitle />
    </div>
    <div class="row">
      <q-space />
      <div class="col-xl-4 col-lg-4 col-md-5 col-sm-6 col-xs-12">
        <div class="q-pa-md">
          <q-form @submit="submitForm()">
            <q-card flat style="border-radius: 20px;">
              <q-card-section>
                <div class="text-h6 text-bbw-blue text-bold">
                  Escríbenos por WhatsApp
                </div>
              </q-card-section>
              <q-separator />
              <q-card-section>
                <q-input
                  label="Nombre"
                  filled
                  v-model="form.name"
                  :rules="[(val) => !!val || 'Este campo es requerido']"
                  class="q-mb-sm"
                  color="blue-9"
                />
                <q-input
                  label="Apellido"
                  filled
                  v-model="form.lastName"
                  :rules="[(val) => !!val || 'Este campo es requerido']"
                  class="q-mb-sm"
                  color="blue-9"
                />
                <q-input
                  label="Correo"
                  filled
                  v-model="form.email"
                  type="email"
                  color="blue-9"
                  :rules="[
                    (val) => !!val || 'Este campo es requerido',
                    (val) =>
                      validEmail.test(val) || 'Formato de correo incorrecto',
                  ]"
                  class="q-mb-sm"
                />
                <q-input
                  label="Mensaje"
                  filled
                  type="textarea"
                  color="blue-9"
                  rows="4"
                  v-model="form.message"
                  :rules="[(val) => !!val || 'Este campo es requerido']"
                  class="q-mb-sm"
                />
              </q-card-section>
              <q-separator />
              <q-card-actions align="right" class="q-px-md">
                <q-btn
                  :disable="loading"
                  color="bbw-pink"
                  rounded
                  class="q-px-md text-bold"
                  unelevated
                  push
                  type="submit"
                >
                  <div v-if="!loading">
                    <q-icon name="fab fa-whatsapp" class="q-mr-sm" />
                    Enviar
                  </div>
                  <q-spinner-dots v-else color="white" size="1em" />
                </q-btn>
              </q-card-actions>
            </q-card>
          </q-form>
        </div>
      </div>
      <q-space />
    </div>
  </q-page>
</template>

<script>
import BBWTitle from "@/components/BBWTitle";
export default {
  data() {
    return {
      form: {
        name: "",
        lastName: "",
        email: "",
        message: "",
      },
      loading: false,
      validEmail: /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/,
    };
  },
  methods: {
    async submitForm() {
      this.loading = true;
      await this.sendToGoogleSheets();
      setTimeout(() => {
        window.location.href = `https://wa.me/${this.returnWhatsappNo()}?text=${this.returnFormattedMessage()}`;
        this.loading = false;
      }, 500);
    },
    returnWhatsappNo() {
      switch (this.$route.params.countryId) {
        case "pa":
          return "50761358727";
        case "gt":
          return "50246722508";
        case "co":
          return "573222660774";
        case "pe":
          return "51932471962";
        case "cr":
          return "50661815936";
        case "py":
          return "595985755570";
      }
    },
    returnSheetUrl() {
      switch (this.$route.params.countryId) {
        case "pa":
          return "https://script.google.com/macros/s/AKfycbxKfp3G9IfhQH1plkDDU_hsbN-gWjsUrryrHRWdQv0ga4EJUXDck1gHaFUsqneqEqBB/exec";
        case "gt":
          return "https://script.google.com/macros/s/AKfycbzJu1cNmOjQ4DIf07-lzo2A2Pp2404CF8lB6g_02iMhgTioyAVot_bcA3NAkbA5WMEx/exec";
        case "co":
          return "https://script.google.com/macros/s/AKfycbz4x4Ceum5YlehVOg5J7u0-N4v6cDLgl4_eQI4oZp5hMdYi7D26hqVD1Ky3TxUBCXgI9g/exec";
        case "pe":
          return "https://script.google.com/macros/s/AKfycbxOM-ZCEhJWI3iR6xl4YWO0k0JTH3lpW8eeCi3jS7TMr--UjWBsTc1AFdnpD_fNAfriQQ/exec";
        case "cr":
          return "https://script.google.com/macros/s/AKfycby_ZXyiy7zX3ZCWM6B6Vc2MM6_3TgXMOVG-L4t9RWDu2Ko4yyVSWosWRUg6yYtWxKyJvA/exec";
        case "py":
          return "https://script.google.com/macros/s/AKfycbxJSD1FyC5l5GDv-t8Xcnl2IJ63tvfRhVWdQZHGpmO4vNaf_tspS0wvoAVuQFtGC1-iTA/exec";
      }
    },
    async sendToGoogleSheets() {
      let data = {
        name: this.form.name,
        lastName: this.form.lastName,
        email: this.form.email,
        timestamp: new Date(),
      };
      var url = this.returnSheetUrl();
      var xhr = new XMLHttpRequest();
      xhr.open("POST", url);
      xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      var encoded = Object.keys(data)
        .map(function(k) {
          return encodeURIComponent(k) + "=" + encodeURIComponent(data[k]);
        })
        .join("&");
      await xhr.send(encoded);
    },
    returnFormattedMessage() {
      return `*Nombre:* ${this.form.name} ${this.form.lastName}%0D*Correo:* ${this.form.email}%0D%0D*Mensaje:* ${this.form.message}`;
    },
  },
  components: {
    BBWTitle,
  },
};
// https://wa.me/50761358727
// https://wa.me/50246722508
// https://wa.me/573222660774
// https://wa.me/51932471962
// https://wa.me/50661815936
// https://wa.me/595985755570
// https://script.google.com/macros/s/AKfycbz7eqYhYAG0as0N4_tZ2yOpgLNl3Tp7NKHzGpwqwiK4oUrnnEo2_Eaqn2zgprzoclfA/exec
</script>

<style scoped>
.gingham-banner {
  background-image: url("https://bathbody.vteximg.com.br/arquivos/290620-gingham-pattern.svg");
}

.text-bbw-blue {
  color: #005294;
}
.bg-bbw-blue {
  background: #005294;
}

.text-bbw-pink {
  color: #ed008e;
}
.bg-bbw-pink {
  background: #ed008e;
}
</style>
