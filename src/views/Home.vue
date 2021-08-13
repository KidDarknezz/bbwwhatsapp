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
          return "https://script.google.com/macros/s/AKfycbx6SylMkT--ZiM9XMy_zCpq2J3Rz5wU7UDusu_5hZcWbp0Q99Ccu_Q5h13j3lPcgJls/exec";
        case "co":
          return "https://script.google.com/macros/s/AKfycbzsz3DO1r80YeJTYCW5H699znS742bUpbeGLjANVsdFB5IwcSlspHt5Das8E0WBVW4HKg/exec";
        case "pe":
          return "https://script.google.com/macros/s/AKfycbzgt7hy9WdFB9srpdejmsNtD2HNgN2OIG5VMuGEHLvjmKRivyRq4jVW_Wd2SEJlckI5Vg/exec";
        case "cr":
          return "https://script.google.com/macros/s/AKfycbypMLyOmfzY9RfTSYXLm1F4lZDyIOXIN8GYuv64xAvr8G5fM8HKpPuYThTdOrCFhoOn/exec";
        case "py":
          return "https://script.google.com/macros/s/AKfycbw27P2rRJVEh0xlFJD4CUqtu1to4Hvc-WWhzKiHf7w_62fdaQAOBke1UBfqFZ-cg4eTRA/exec";
      }
    },
    async sendToGoogleSheets() {
      let data = {
        name: this.form.name,
        lastName: this.form.lastName,
        email: this.form.email,
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
// PA - https://wa.me/50761358727
// GT - https://wa.me/50246722508
// CO - https://wa.me/573222660774
// PE - https://wa.me/51932471962
// CR - https://wa.me/50661815936
// PY - https://wa.me/595985755570
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
