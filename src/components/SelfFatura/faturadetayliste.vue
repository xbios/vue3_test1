<template>
  <div>
    <faturadetay v-if="DetayDialog2" :DetayDialog2.sync="DetayDialog2" :detayEditedItem="DetayEdit.editedItem" @emit_sfatdet_kaydet="sfatdet_kaydet($event)"> </faturadetay>

    <v-card :key="componentDetayKey" class="ma-1 pb-0">
      <!-- <v-card flat> -->
      <!-- TODO: 3  import fatura-Detay-Giris -->

      <v-card-text class="pa-1 grey lighten-2">
        <v-data-table
          name="sfatdet"
          dense
          hide-default-footer
          mobile-breakpoint="0"
          height="35vh"
          class="elevation-0 row-pointer"
          :headers="dsListe.FieldNames"
          :items="dsListe.DataRows"
          :items-per-page="250"
          :loading="loadingTable"
          loading-text="Yükleniyor... Lütfen Bekleyin."
        >
          <template v-slot:[`item.FRUNVAN`]="{ item }">
            <span class="green--text fw-bold">{{ item.FRUNVAN }}</span>
            <!-- <span v-if="item.DoktorKodu === getUser" class="green--text fw-bold">{{ item.DoktorKodu }}</span> -->
            <!-- <span v-else>{{ item.DoktorKodu }}</span> -->
          </template>
          <!-- :ripple="true" -->
          <!-- @click:row="GridClick()" -->
          <template v-slot:[`item.Sec`]="{ item }">
            <v-btn icon @click="Edit(item)" small>
              <v-icon class="blue--text" small>mdi-pencil</v-icon>
            </v-btn>
            <v-btn icon @click="Sil($event, item)" small>
              <v-icon class="error--text" smal>mdi-delete</v-icon>
            </v-btn>
          </template>
          <template v-slot:[`item.FDFIYAT`]="{ item }">
            <span class="grey--text fw-bold">{{ vueNumberFormat(item.FDFIYAT, {}) }}</span>
          </template>
          <template v-slot:[`item.FDTUTAR`]="{ item }">
            <span class="grey--text fw-bold">{{ vueNumberFormat(item.FDTUTAR, {}) }}</span>
          </template>
          <!-- <template v-slot:[`body.append`]>
            <tr class="sticky-table-footer">
              <td v-text="''" />
              <td v-text="''" />
              <td v-text="''" />
              <td v-text="''" />
              <td v-text="''" />
              <td v-text="'Toplamlar'" />
              <td v-text="''" />
              <td v-text="''" />
              <td v-text="''" />
              <td v-text="detayTotal.toplam" />
            </tr>
          </template> -->
          <!-- <template v-slot:[`item.IslemAdi`]="{ item }">
                          <div class="table-short-text">
                            {{ item.IslemAdi }}
                          </div>
                        </template>
                        <template v-slot:[`item.EskiIslemAdi`]="{ item }">
                          <div class="table-short-text">
                            {{ item.EskiIslemAdi }}
                          </div>
                        </template> -->
        </v-data-table>
      </v-card-text>
      <!-- <v-col>
          <v-row>
          </v-row>
        </v-col> -->
      <!-- </v-card> -->
      <v-card-actions class="pa-2">
        <!-- <v-spacer /> -->

        <!-- <div class="d-flex flex-column small mb-0 text-right">
          <v-card class="mb-1 pa-0 elevation-1">
            <v-chip class="ma-0" label> Toplam </v-chip>
            <v-chip class="ma-0" label color="blue" text-color="white"> {{ detayTotal.toplam }} </v-chip>
          </v-card>
          <v-card class="mb-1 pa-0 elevation-1 blue">
            <v-chip class="ma-0" label color="blue" text-color="white"> DENEM </v-chip>
          </v-card>
          <v-card class="mb-1 pa-0 elevation-1 blue">
            <v-chip class="ma-0" label color="blue" text-color="white"> DENEM </v-chip>
          </v-card>
        </div> -->

        <v-row>
          <v-col col="12" sm="12">
            <v-row no-gutter>
              <v-col sm="4">
                <v-chip class="ma-0" color="orange lighten-1" text-color="white">
                  <span>
                    <v-icon small left> mdi-information-outline </v-icon>
                    {{ masterid }} , {{ formTitle }} / {{ DetayEdit.editedIndex }}, {{ DetayEdit.editedItem.FDID }}
                  </span>
                </v-chip>
              </v-col>
              <v-col sm="4"> </v-col>
              <v-col sm="4">
                <v-card class="mx-auto" color="grey lighten-3" dark rounded="md">
                  <v-row class="ma-0 pa-0" style="overflow: hidden">
                    <!-- Add `overflow: hidden;` here! -->

                    <!-- I've also tweaked this first column -->
                    <v-col cols="6" class="pa-1">
                      <!-- <div class="fill-height d-flex justify-center align-center">this should be different color</div> -->
                      <div class="d-flex flex-column small mb-0 text-right">
                        <v-card class="mb-1 px-3 elevation-0"> Toplam </v-card>
                        <v-card class="mb-1 px-3 elevation-0"> Toplam İskonto </v-card>
                        <v-card class="mb-1 px-3 elevation-0"> Net Toplam </v-card>
                        <v-card class="mb-1 px-3 elevation-0"> Toplam KDV </v-card>
                        <v-card class="mb-1 px-3 elevation-0"> Genel Toplam </v-card>
                      </div>
                    </v-col>

                    <v-col cols="6" class="pa-1 white">
                      <!-- v-card-title and v-card-action -->
                      <div class="d-flex flex-column small mb-0 text-right">
                        <v-card class="mb-1 px-3 elevation-0" color="blue darken-1"> {{ vueNumberFormat(detayTotal.toplam, {}) }}</v-card>
                        <v-card class="mb-1 px-3 elevation-0" color="blue darken-1"> {{ vueNumberFormat(detayTotal.toplamiskonto, {}) }} </v-card>
                        <v-card class="mb-1 px-3 elevation-0" color="blue darken-1"> {{ vueNumberFormat(detayTotal.toplamnet, {}) }} </v-card>
                        <v-card class="mb-1 px-3 elevation-0" color="blue darken-1"> {{ vueNumberFormat(detayTotal.kdv, {}) }} </v-card>
                        <v-card class="mb-1 px-3 elevation-0" color="blue darken-1"> {{ vueNumberFormat(detayTotal.geneltoplam, {}) }} </v-card>
                      </div>
                    </v-col>
                  </v-row>
                </v-card>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import moment from "moment";
import sensorApi from "@/api/sensorAPI";
import { axiosConf2 } from "@/helpers/helpers";
import { sfatdet } from "./data";
import faturadetay from "Components/SelfFatura/faturadetay";

export default {
  name: "faturadetayliste",
  props: ["masterid", "componentDetayKey"],
  components: {
    faturadetay,
  },
  data() {
    return {
      loadingTable: false,
      DetayDialog2: false,
      dsListe: {
        DataRows: [],
        FieldNames: [],
      },
      DetayEdit: {
        editedIndex: -1,
        editedItem: {},
      },
      detayTotal: {},
    };
  },
  watch: {
    componentDetayKey: function (newVal, oldVal) {
      console.log("componentDetayKey changed: ", newVal, " | was: ", oldVal);
      this.DetayEdit.editedIndex = -1;
      this.DetayEdit.editedItem = Object.assign({}, sfatdet);
      this.DetayDialog2 = true;
    },
    masterid: function (newVal, oldVal) {
      console.log("masterid changed: ", newVal, " | was: ", oldVal);
      if (newVal != oldVal) {
        if (oldVal == -1 && newVal > 0) {
          this.DetayEdit.editedIndex = -1;
          this.DetayEdit.editedItem = Object.assign({}, sfatdet);
          this.DetayDialog2 = true;
        }
      }
    },
  },

  async created() {
    if (this.masterid != null) {
      this.listele();
    }
  },

  methods: {
    async closeDialog() {
      console.log("closeDialog");
    },

    async Sil(e, item) {
      // console.log(e);
      console.log(item);
      if (item.FDID != undefined && (await this.$root.$confirm("Satır Sil!", "Silinecek emin misiniz?", { color: "red" }))) {
        console.log("Evet");
      }
    },

    Edit(item) {
      this.DetayEdit.editedIndex = this.dsListe.DataRows.indexOf(item);
      this.DetayEdit.editedItem = JSON.parse(JSON.stringify(item));
      this.DetayDialog2 = true;

      // TODO: emit_sfatdet_edit ->
      this.$emit("emit_sfatdet_edit");
    },

    // TODO: fatura emit_sfatdet_kaydet BBB
    async sfatdet_kaydet(event) {
      let data_event = JSON.parse(JSON.stringify(event));
      if (this.masterid == -1) {
        this.$appmesaj(true, "masterid == -1 !");
        console.log("masterid: " + this.masterid);
        return;
      }
      //! NEW
      if (this.DetayEdit.editedIndex == -1) {
        //this.DetayEdit.editedItem["FTID"] = this.masterid;
        data_event["FTID"] = this.masterid;
        data_event["FDSTKODU"] = data_event["_STOKKOD"];
        data_event["FDSTADI"] = data_event["_STOKADI"];

        delete data_event["_STOKADI"]; //
        delete data_event["_STOKKOD"]; //
        await sensorApi
          .setData({
            table: "sfatdet",
            action: "I",
            crud: [],
            data: data_event,
          })
          .then((response) => {
            this.$appmesaj(true, response.mesaj);
            event.FDID = response.id;
            this.dsListe.DataRows.push(event);
            this.DetayDialog2 = false;
            this.detayTotalHesapla();
          })
          .catch((error) => {
            this.$appmesaj(true, error.mesaj, "error");
          });
      }

      //! EDIT
      if (this.DetayEdit.editedIndex > -1) {
        //event["EditUser"] = this.getUser;
        //event["EditDate"] = moment().format("YMMDD HH:mm:ss");
        //event["EditComp"] = "WEB_HBYS";
        console.log(moment().format("YMMDD HH:mm:ss"));
        data_event["FDSTKODU"] = data_event["_STOKKOD"];
        data_event["FDSTADI"] = data_event["_STOKADI"];

        delete data_event["FTID"];
        delete data_event["_STOKADI"];
        delete data_event["_STOKKOD"];
        await sensorApi
          .setData({
            table: "sfatdet",
            action: "U",
            crud: [
              {
                key: "FDID",
                value: event.FDID,
              },
            ],
            data: data_event,
          })
          .then((response) => {
            this.$appmesaj(true, response.mesaj);
            Object.assign(this.dsListe.DataRows[this.DetayEdit.editedIndex], event);
            this.DetayDialog2 = false;
            this.detayTotalHesapla();
          })
          .catch((error) => {
            this.$appmesaj(true, error.mesaj, "error");
          });
      }
    },

    async listele() {
      //debugLog("getSelfData.php: respFaturaDetay :  ", null, null, "SELF");
      if (this.loadingTable == true) {
        this.$notify({
          text: "Lütfen Seçimin yüklenemsini bekleyiniz!",
          type: "warn",
        });
        return;
      }
      this.loadingTable = true;
      const sqltext = axiosConf2(["K", "Id"], ["sfatdet", this.masterid], "sp_WebSelf_Detay");
      //console.log(": " + JSON.stringify(sqltext, null, 2));
      await sensorApi
        .getData({
          Action: "getSelfData.php",
          SelectYeni: sqltext,
          Type: "Liste",
          SetKey: "respFaturaDetay",
        })
        .then((response) => {
          this.$notify(response.mesaj);
          let layload = JSON.parse(JSON.stringify(this.getResponse["respFaturaDetay"]));
          let fields = [
            {
              text: "Seç",
              value: "Sec",
              class: "blue lighten-2",
              width: "20",
            },
            {
              text: "No",
              value: "FDID",
              class: "blue lighten-2",
              width: "50",
            },
            // {
            //   text: "Stok ID",
            //   value: "FDSTID",
            //   class: "blue lighten-2",
            // },
            {
              text: "Stok Kodu",
              value: "_STOKKOD",
              class: "blue lighten-2",
            },
            {
              text: "Stok Adı",
              value: "_STOKADI",
              class: "blue lighten-2",
            },
            {
              text: "Miktar",
              value: "FDMIKTAR",
              class: "blue lighten-2",
            },
            {
              text: "Birim",
              value: "FDBIRIM",
              class: "blue lighten-2",
            },
            {
              text: "Kdv %",
              value: "FDKDV",
              class: "blue lighten-2",
              align: "right",
            },
            {
              text: "İsk.%",
              value: "FDINDIRIMYUZDE",
              class: "blue lighten-2",
              align: "right",
            },
            {
              text: "Fiyat",
              value: "FDFIYAT",
              class: "blue lighten-2",
              align: "right",
            },

            {
              text: "Tutar",
              value: "FDTUTAR",
              class: "blue lighten-2",
              align: "right",
            },
          ];
          this.dsListe = {
            DataRows: layload.DataRows,
            FieldNames: fields,
            //FieldNames: layload.FieldNames,
          };
          //console.log("api response >>> " + JSON.stringify(this.dsListe, null, 2));
          this.detayTotalHesapla();
        })
        .catch((error) => {
          this.$notify({
            text: error.mesaj,
            type: "error",
          });
        });
      this.loadingTable = false;
    },
    async detayTotalHesapla() {
      let top = {
        toplam: 0,
        toplamiskonto: 0,
        toplamnet: 0,
        kdv: 0,
        geneltoplam: 0,
      };
      console.log("::" + moment().format("Y-MM-DD HH:mm:ss:SSS"));
      await this.dsListe.DataRows.map((item) => {
        top.toplam = top.toplam + Number(item.FDTUTAR);
        top.toplamiskonto = top.toplamiskonto + Number(item.FDINDIRIM);
        top.kdv = top.kdv + ((Number(item.FDTUTAR) - Number(item.FDINDIRIM)) * Number(item.FDKDV)) / 100;
      });
      console.log("::" + moment().format("Y-MM-DD HH:mm:ss:SSS"));
      top.toplamnet = top.toplam - top.toplamiskonto;
      top.geneltoplam = top.toplamnet + top.kdv;
      this.detayTotal = top;
      // TODO: emit_sfatdet_total ->
      this.$emit("emit_sfatdet_total", this.detayTotal);
    },
  },

  computed: {
    ...mapGetters(["getResponse", "getUser", "darkMode", "dsGorevTanimlar"]),
    formTitle() {
      return this.DetayEdit.editedIndex === -1 ? "I" : "U";
    },
    // detayTotal() {
    //   let top = {
    //     matrah: 0,
    //     kdv: 0,
    //     toplam: 0,
    //   };
    //   this.dsListe.DataRows.map((item) => {
    //     top.matrah = top.matrah + Number(item.FDFIYAT);
    //     top.kdv = top.kdv + Number(item.FDKDV);
    //     top.toplam = top.toplam + Number(item.FDTUTAR);
    //   });
    //   return top;
    // },
  },
};
</script>

<style scoped>
.sticky-table-footer td {
  font-weight: bold;
  position: sticky;
  bottom: 0;
  background-color: #aeaeae;
  border-top: thin solid rgba(0, 0, 0, 0.52);
  text-align: right !important;
  border: 2px solid rgb(255, 255, 255);
  /* margin-right: 1em; */
}
</style>
