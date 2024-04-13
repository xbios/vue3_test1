<template>
  <div>
    <v-card class="mx-4 my-2" :key="componentMasterKey">
      <!-- card title -->
      <v-toolbar rounded dense dakr class="blue darken-2 text-center">
        <v-toolbar-title class="white--text"> <v-icon left>mdi-note</v-icon>Fatura Giris </v-toolbar-title>
        <v-spacer />

        <v-chip class="ma-0" color="pink" text-color="white">
          <span>
            <v-icon left small> mdi-database </v-icon>
            SFATMASTID : {{ masterEditedItem.SFATMASTID }}
          </span>
        </v-chip>

        <v-chip class="ma-0" color="green" text-color="white">
          <span>
            <v-icon left small> mdi-file-account </v-icon>
            FTCRID : {{ masterEditedItem.FTCRID }}
          </span>
        </v-chip>

        <v-spacer />

        <v-btn color="red darken-1" class="mr-2" small @click="closeDialog()">
          <span class="white--text">
            <v-icon center>mdi-close</v-icon>
          </span>
        </v-btn>
        <v-btn color="primary" class="mr-2" small dark @click="Kaydet('yeni_satir')"> <v-icon
            left>mdi-file-document</v-icon>Yeni Satır </v-btn>
        <v-btn color="primary" small @click="Kaydet('kaydet')"> <v-icon left>mdi-content-save</v-icon>Fatura Kaydet
        </v-btn>
      </v-toolbar>

      <!-- card text -->
      <v-card-text class="grey lighten-3 pa-1">
        <!-- <v-container class="mt-2 grey lighten-5"> -->
        <v-row>
          <v-col col="12" sm="12">
            <!-- Form start -->
            <!-- <v-row no-gutter> -->
            <!-- <v-col sm="3">
                  <v-card class="pa-4" outlined tile>
                    <v-chip class="ma-0" color="pink" text-color="white">
                      <span>
                        <v-icon left> mdi-database </v-icon>
                        SFATMASTID : {{ masterEditedItem.SFATMASTID }}
                      </span>
                    </v-chip>
                  </v-card>
                </v-col> -->
            <!-- </v-row> -->
            <v-row no-gutters>
              <v-col cols="2" sm="2">
                <v-card class="pa-0" outlined tile>
                  <v-menu dense :close-on-content-click="true" :nudge-right="0" transition="scale-transition" offset-y>
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field v-model="compSecilenFtTarih" label="Fatura Tarihi" prepend-icon="mdi-calendar-today"
                        readonly locale="tr-TR" v-bind="attrs" v-on="on" hide-details class="ma-1" />
                    </template>
                    <v-date-picker v-model="masterEditedItem.FTTARIH" no-title :first-day-of-week="1"
                      color="red accent-3" locale="tr-TR" />
                  </v-menu>
                </v-card>
              </v-col>
              <v-col sm="2">
                <v-card class="pa-0" outlined tile>
                  <v-text-field v-model="masterEditedItem.FTBELNO" label="Belge No" hide-details class="ma-1" />
                </v-card>
              </v-col>
              <v-col sm="2">
                <v-card class="pa-0" outlined tile>
                  <v-text-field v-model="masterEditedItem._CRKOD" label="Cari Kod" hide-details class="ma-1" />
                </v-card>
              </v-col>
              <v-col sm="6">
                <v-card class="pa-0" outlined tile>
                  <v-text-field v-model="masterEditedItem._CRISIM" label="Cari İsim" hide-details class="ma-1 red--text"
                    :append-icon="'mdi-comment-question-outline'" @click:append="getCari()" />
                </v-card>
              </v-col>
              <v-col sm="2">
                <v-card class="pa-0" outlined tile>
                  <v-text-field v-model="masterEditedItem.FTBILGI" label="Bilgi" hide-details class="ma-1" />
                </v-card>
              </v-col>
              <v-col sm="2">
                <v-card class="pa-0" outlined tile>
                  <v-text-field v-model="masterEditedItem.GUNCELLEMEDATE" label="EditDate" hide-details class="ma-1" />
                </v-card>
              </v-col>
            </v-row>
            <v-row no-gutter> </v-row>
            <!-- Form End -->
          </v-col>
        </v-row>
        <!-- </v-container> -->
      </v-card-text>
    </v-card>

    <!-- 2. KISIM -->
    <v-card class="mx-4 my-0 pa-0">
      <!-- <v-toolbar rounded dense dark color="blue lighten-2">
        <v-toolbar-title dense> <v-icon left>mdi-apps</v-icon>Fatura Detay </v-toolbar-title>
        <v-spacer />
        <v-btn color="primary" class="mr-0" small dark @click="Kaydet('yeni_satir')"> <v-icon left>mdi-file-document</v-icon>Yeni Satır </v-btn>
      </v-toolbar> -->

      <v-card-text class="grey lighten-3 pa-1 mx-0">
        <!-- faturadetayliste slot -->
        <slot name="faturadetaylisteslot"> </slot>
        <!-- faturadetayliste slot -->
      </v-card-text>

      <v-card-actions class="px-4">
        <v-row>
          <v-col col="12" sm="12">
            <v-row no-gutter>
              <v-col sm="6"> </v-col>
              <v-col sm="2"> </v-col>
              <v-col sm="2"> </v-col>
              <v-col sm="2"> </v-col>
            </v-row>
          </v-col>
        </v-row>
        <!-- <v-spacer /> -->
      </v-card-actions>
    </v-card>

    <secim v-if="secimDialog" :secimDialog.sync="secimDialog" :mtSecim="mtSecim" @emit_cari="set_cari($event)"></secim>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import moment from "moment";
import secim from "Components/SelfUtils/secim";

export default {
  name: "fatura",
  props: ["masterEditedItem", "DetayDialog", "componentMasterKey"],
  components: {
    secim,
  },
  data() {
    return {
      secimDialog: false,
      mtSecim: "",
      masterEditedItemOld: null,
    };
  },
  watch: {
    componentMasterKey: function (newVal, oldVal) {
      console.log("componentMasterKey changed: ", newVal, " | was: ", oldVal);
    },
    masterEditedItem: function () {
      if (JSON.stringify(this.masterEditedItemOld) != JSON.stringify(this.masterEditedItem)) {
        console.log("::: DEĞİŞTİ :::");
      }
    },
  },
  async created() {
    this.masterEditedItemOld = Object.assign({}, this.masterEditedItem);
  },
  methods: {
    handleScroll(event) {
      console.log("handleScroll");
      alert("ok");
      if (event.target.scrollTop < 250) {
        if (event.target.scrollTop > this.currentScroll) {
          this.$refs.handleScroll.scrollTop = this.$refs.anamnezForm.$el.offsetTop;
        }
      }
      this.currentScroll = event.target.scrollTop;
    },

    // TODO: fatura emit_sfatmast_kaydet AAA ->
    // 0 kaydet, 1 yeni_satir
    Kaydet(tip_value) {
      console.log("type:" + tip_value); //0  1
      if (JSON.stringify(this.masterEditedItemOld) == JSON.stringify(this.masterEditedItem) && tip_value == "kaydet") {
        this.$appmesaj(true, "Bir değişiklik yok !");
        this.$emit("update:DetayDialog", false);
        return;
      }
      let emit_data = {
        masterItem: this.masterEditedItem,
        type: tip_value,
      };
      this.$emit("emit_sfatmast_kaydet", emit_data);
    },

    escKey() {
      this.closeDialog();
    },
    async closeDialog() {
      //if (this.formControl)
      if (JSON.stringify(this.masterEditedItemOld) === JSON.stringify(this.masterEditedItem)) {
        this.$emit("update:DetayDialog", false);
      } else {
        if (
          await this.$root.$confirm("Kaydetmeden çıkıyorsunuz!", "Çıkmak istediğinize emin misiniz?", {
            color: "red",
          })
        ) {
          this.$emit("update:DetayDialog", false);
        }
      }
    },

    getCari() {
      console.log("getCari");
      this.mtSecim = "mtcari";
      this.secimDialog = true;
    },
    set_cari(item) {
      console.log(item + ": " + JSON.stringify(item, null, 2));
      this.masterEditedItem.FTCRID = item.Id;
      this.masterEditedItem._CRKOD = item.Kod;
      this.masterEditedItem._CRISIM = item.Aciklama;
      this.secimDialog = false;
    },
    FormatDateTr(value) {
      const Tarihtr = moment(value, "YYYY-MM-DD");
      if (value) {
        return moment(String(Tarihtr)).format("DD.MM.YYYY");
      }
    },
  },
  computed: {
    compSecilenFtTarih() {
      return this.FormatDateTr(this.masterEditedItem.FTTARIH);
    },
    ...mapGetters(["getResponse", "getUser", "darkMode"]),
  },
};
</script>

<style scoped></style>
