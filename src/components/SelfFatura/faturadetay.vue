<template>
  <!-- <v-dialog scrollable v-model="DetayDialog2" @scroll="handleScroll" ref="handleScroll" @close="closeDialog()" @keydown.esc="escKey" persistent> -->
  <v-card class="my-0 mx-6">
    <!-- <v-toolbar rounded dense dark class="blue lighten-2 text-center">
      <v-toolbar-title class="white--text"> <v-icon left>mdi-note</v-icon>Fatura Detay Giris </v-toolbar-title>
      <v-spacer />

      <v-spacer />
    </v-toolbar> -->

    <!-- card text -->
    <v-card-text class="pa-4 indigo lighten-4">
      <!-- <v-container class="mt-3 grey lighten-5"> -->
      <v-row>
        <v-col col="12" sm="12">
          <!-- Form start -->
          <!-- <v-row no-gutters>
            <v-col sm="2">
              <v-card class="pa-4" outlined tile>
                <v-chip class="ma-0" color="pink" text-color="white">
                  <span>
                    <v-icon left> mdi-database </v-icon>
                    FDID : {{ detayEditedItem.FDID }}
                  </span>
                </v-chip>
              </v-card>
            </v-col>
          </v-row> -->
          <!-- <v-row no-gutter>
              <v-col sm="2">
                <v-card class="pa-0" outlined tile>
                  <v-text-field v-model="detayEditedItem.FDSTID" label="Stok ID" hide-details class="ma-1" />
                </v-card>
              </v-col>
            </v-row> -->
          <v-row no-gutters>
            <v-col sm="2">
              <v-card class="pa-0" outlined tile>
                <v-text-field v-model="detayEditedItem._STOKKOD" label="Stok Kod" hide-details class="ma-1" />
              </v-card>
            </v-col>
            <v-col sm="2">
              <v-card class="pa-0" outlined tile>
                <v-text-field
                  v-model="detayEditedItem._STOKADI"
                  label="Stok Adı"
                  hide-details
                  class="ma-1 red--text"
                  :append-icon="'mdi-comment-question-outline'"
                  @click:append="getStok()"
                />
              </v-card>
            </v-col>
            <v-col sm="1">
              <v-card class="pa-0" outlined tile>
                <v-text-field v-model="detayEditedItem.FDMIKTAR" label="Miktar" hide-details class="ma-1" />
              </v-card>
            </v-col>
            <v-col sm="1">
              <v-card class="pa-0" outlined tile>
                <v-text-field v-model="detayEditedItem.FDBIRIM" label="Birim" hide-details class="ma-1" />
              </v-card>
            </v-col>

            <v-col sm="1">
              <v-card class="pa-0" outlined tile>
                <v-text-field v-model="detayEditedItem.FDKDV" label="Kdv" hide-details class="ma-1" />
              </v-card>
            </v-col>
            <v-col sm="1">
              <v-card class="pa-0" outlined tile>
                <v-text-field v-model="detayEditedItem.FDINDIRIMYUZDE" label="İndirim %" hide-details class="ma-1" />
              </v-card>
            </v-col>
            <v-col sm="2">
              <v-card class="pa-1" outlined tile>
                <!-- <v-text-field v-model="detayEditedItem.FDFIYAT" label="Fiyat" hide-details class="ma-1" /> -->
                <v-currency-field v-model="detayEditedItem.FDFIYAT" label="Fiyat" class="ma-0" />
              </v-card>
            </v-col>
            <v-col sm="2">
              <v-card class="pa-1" outlined tile>
                <!-- <v-text-field v-model="detayEditedItem.FDTUTAR" label="Tutar" hide-details class="ma-1" /> -->
                <v-currency-field v-model="detayEditedItem.FDTUTAR" label="Tutar" class="ma-0" />
              </v-card>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
      <!-- </v-container> -->
    </v-card-text>

    <!-- card action -->
    <v-card-actions class="pa-2 indigo lighten-4">
      <v-chip class="ma-0" color="pink lighten-1" text-color="white">
        <span>
          <v-icon small left> mdi-information-outline </v-icon>
          İndirim Toplam (FDINDIRIM) : {{ detayEditedItem.FDINDIRIM }} - İndirimli Fiyat (INDIRIMLIFIYAT) : {{ detayEditedItem.INDIRIMLIFIYAT }}
        </span>
      </v-chip>
      <v-spacer />
      <v-chip class="ma-0" color="pink" text-color="white">
        <span>
          <v-icon left small> mdi-database </v-icon>
          FDID : {{ detayEditedItem.FDID }}
        </span>
      </v-chip>
      <v-chip class="ma-0" color="green" text-color="white">
        <span>
          <v-icon left small> mdi-database </v-icon>
          FDSTID : {{ detayEditedItem.FDSTID }}
        </span>
      </v-chip>
      <v-spacer />
      <v-btn color="red darken-1" small @click="closeDialog()">
        <span class="white--text">
          <v-icon center>mdi-close</v-icon>
        </span>
      </v-btn>
      <v-btn color="primary" small @click="Kaydet"> <v-icon left>mdi-play-protected-content</v-icon>Satır Kaydet </v-btn>
    </v-card-actions>

    <secim v-if="secimDialog" :secimDialog.sync="secimDialog" :mtSecim="mtSecim" @emit_stok="set_stok($event)"></secim>
  </v-card>
  <!-- </v-dialog> -->
</template>

<script>
import secim from "Components/SelfUtils/secim";
import VCurrencyField from "Components/VCurrencyField";
export default {
  name: "faturadetay",
  props: ["detayEditedItem", "DetayDialog2"],
  components: {
    secim: secim,
    VCurrencyField,
  },
  data() {
    return {
      secimDialog: false,
      mtSecim: "",
    };
  },
  async created() {
    this.detayEditedItemOld = Object.assign({}, this.detayEditedItem);
  },
  watch: {
    "detayEditedItem.FDMIKTAR": function (newVal, oldVal) {
      console.log("FDMIKTAR_old: " + oldVal + "  FDMIKTAR_new: " + newVal);
      this.detayEditedItem.FDTUTAR = this.detayEditedItem.FDFIYAT * newVal;
      this.detayEditedItem.FDINDIRIM = (this.detayEditedItem.FDTUTAR * newVal) / 100;
    },
    "detayEditedItem.FDFIYAT": function (newVal, oldVal) {
      console.log("FDFIYAT_old: " + oldVal + "  FDFIYAT_new: " + newVal);
      this.detayEditedItem.FDTUTAR = this.detayEditedItem.FDMIKTAR * newVal;
      this.detayEditedItem.FDINDIRIM = (this.detayEditedItem.FDTUTAR * this.detayEditedItem.FDINDIRIMYUZDE) / 100;
      let _FdFiyatIndirim = (this.detayEditedItem.FDFIYAT * this.detayEditedItem.FDINDIRIMYUZDE) / 100;
      this.detayEditedItem.INDIRIMLIFIYAT = this.detayEditedItem.FDFIYAT - _FdFiyatIndirim;
    },
    "detayEditedItem.FDTUTAR": function (newVal, oldVal) {
      console.log("FDTUTAR_old: " + oldVal + "  FDTUTAR_new: " + newVal);
      this.detayEditedItem.FDFIYAT = newVal / this.detayEditedItem.FDMIKTAR;
    },
    "detayEditedItem.FDINDIRIMYUZDE": function (newVal, oldVal) {
      console.log("FDINDIRIMYUZDE_old: " + oldVal + "  FDINDIRIMYUZDE_new: " + (this.detayEditedItem.FDTUTAR * newVal) / 100);
      this.detayEditedItem.FDINDIRIM = (this.detayEditedItem.FDTUTAR * newVal) / 100;
      let _FdFiyatIndirim = (this.detayEditedItem.FDFIYAT * newVal) / 100;
      this.detayEditedItem.INDIRIMLIFIYAT = this.detayEditedItem.FDFIYAT - _FdFiyatIndirim;
    },
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

    getStok() {
      console.log("getStok");
      this.mtSecim = "mtstok";
      this.secimDialog = true;
    },
    set_stok(item) {
      console.log(item + ": " + JSON.stringify(item, null, 2));
      this.detayEditedItem.FDSTID = item.Id;
      this.detayEditedItem._STOKKOD = item.Kod;
      this.detayEditedItem._STOKADI = item.Aciklama;
      this.secimDialog = false;
    },
    // TODO: fatura emit_sfatdet_kaydet BBB ->
    Kaydet() {
      if (JSON.stringify(this.detayEditedItemOld) !== JSON.stringify(this.detayEditedItem)) {
        this.$emit("emit_sfatdet_kaydet", this.detayEditedItem);
      } else {
        this.$appmesaj(true, "Bir değişiklik yok !");
        this.$emit("update:DetayDialog2", false);
      }
    },
    escKey() {
      this.closeDialog();
    },
    async closeDialog() {
      //if (this.formControl)
      if (JSON.stringify(this.detayEditedItemOld) === JSON.stringify(this.detayEditedItem)) {
        this.$emit("update:DetayDialog2", false);
      } else {
        if (
          await this.$root.$confirm("Kaydetmeden çıkıyorsunuz!", "Çıkmak istediğinize emin misiniz?", {
            color: "red",
          })
        ) {
          this.$emit("update:DetayDialog2", false);
        }
      }
    },
  },
};
</script>

<style scoped></style>
