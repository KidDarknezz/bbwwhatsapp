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
    async sendToGoogleSheets() {
      let data = {
        name: this.form.name,
        lastName: this.form.lastName,
        email: this.form.email,
        timestamp: new Date(),
      };
      var url =
        "https://script.google.com/macros/s/AKfycbz7eqYhYAG0as0N4_tZ2yOpgLNl3Tp7NKHzGpwqwiK4oUrnnEo2_Eaqn2zgprzoclfA/exec";
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
