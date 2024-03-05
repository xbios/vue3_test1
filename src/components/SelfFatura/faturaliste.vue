<template>
  <div>
    <filtre v-if="filtreDialog" :filtreDialog.sync="filtreDialog" :mtFiltre="mtFiltre" @emit_filtre_fatura="set_filtre($event)"></filtre>
    <!-- TODO: 1  import faturalar-Giris -->
    <fatura
      v-if="DetayDialog"
      @emit_sfatmast_kaydet="sfatmast_kaydet($event)"
      :DetayDialog.sync="DetayDialog"
      :masterEditedItem="MasterEdit.editedItem"
      :componentMasterKey="componentMasterKey"
    >
      <!-- TODO: 1  import fatura-Detay -->
      <faturadetayliste
        slot="faturadetaylisteslot"
        v-if="DetayDialog"
        @emit_sfatdet_edit="sfatdet_edit()"
        @emit_sfatdet_total="sfatdet_total($event)"
        :masterid="MasterEdit.masterid"
        :componentDetayKey="componentDetayKey"
      ></faturadetayliste>
    </fatura>

    <!-- TODO: 1  import faturalar -->
    <v-card v-if="!DetayDialog" class="mx-4 my-4 pb-8">
      <v-toolbar rounded dense dark color="blue darken-2">
        <v-toolbar-title>
          <v-icon left>mdi-apps</v-icon>Faturalar : {{ formTitle }} ::
          {{ MasterEdit.editedIndex }}
        </v-toolbar-title>
        <v-spacer />
        <v-text-field v-model="search" append-icon="mdi-magnify" label="Arama" single-line hide-details></v-text-field>
        <v-spacer />
        <v-btn class="mr-2" small color="info" dark @click="getFiltre()"> Filtre </v-btn>
        <v-btn color="success" small @click="listele()"> <v-icon left>mdi-filter</v-icon>Listele </v-btn>
        <v-menu left bottom>
          <template v-slot:activator="{ on }">
            <!-- <v-btn icon v-on="on">
              <v-icon>mdi-menu-down-outline</v-icon>
            </v-btn> -->
            <!-- <v-btn color="red darken-4" class="mr-2" small @click="YeniKayit()"> <v-icon left>mdi-file-document-edit </v-icon>Yeni Fatura </v-btn> -->
            <v-btn v-on="on" color="primary" class="ml-2" small> <v-icon left>mdi-file-document-edit </v-icon>Yeni Fatura </v-btn>
          </template>
          <!-- //TODO: YETKI v-permission -->
          <!-- <v-list v-permission="{ name: 'RaporLokasyonSec.Calistir' }">            -->
          <v-list>
            <v-list-item v-for="(item, index) in yeniKayitMenuitems" :key="index" @click="YeniKayit(item)">
              <v-list-item-title>
                <v-icon left>mdi-view-list</v-icon>
                {{ item.title }}</v-list-item-title
              >
            </v-list-item>
          </v-list>
        </v-menu>
      </v-toolbar>
      <v-card flat>
        <v-card-text class="pa-4">
          <!-- <v-col> -->
          <!-- <v-row> -->
          <!-- hide-default-footer -->
          <v-data-table
            name="sfatmast"
            dense
            fixed-header
            height="65vh"
            mobile-breakpoint="300"
            must-sort
            multi-sort
            :sort-by="['FTTARIH', 'SFATMASTID']"
            :sort-desc="[true, true]"
            :headers="dsListe.FieldNames"
            :items="dsListe.DataRows"
            :search="search"
            :loading="loadingTable"
            loading-text="Yükleniyor... Lütfen Bekleyin."
            class="elevation-1 row-pointer"
            :item-class="ListRowBackground"
            @contextmenu:row="RightMenu"
            @click:row="rowClick"
            :page="pagination.page"
            :pageCount="pagination.pagecount"
            :items-per-page="pagination.pageline"
            :server-items-length.sync="pagination.RecordCount"
            :options.sync="options"
            :footer-props="{
              'items-per-page-text': 'Sayfa:',
              itemsPerPageOptions: [15, 25, 50, 100, -1],
            }"
          >
            <!-- class="elevation-1" -->
            <template v-slot:[`item._CRISIM`]="{ item }">
              <span class="black--text fw-bold">{{ item._CRISIM }}</span>
            </template>
            <template v-slot:[`item.SFATMASTID`]="{ item }">
              <span class="sirano">{{ item.SFATMASTID }}</span>
            </template>
            <template v-slot:[`item.KILITLI`]="{ item }">
              <span v-if="item.KILITLI == 2"> <v-icon class="green--text" small>mdi-lock</v-icon> </span>
              <span v-else> <v-icon class="grey--text" small>mdi-select mdi-dark mdi-inactive</v-icon></span>
              <span v-if="item.FTIPTAL == 1"> <v-icon class="red--text" small>mdi-cancel</v-icon> </span>
              <span v-else> <v-icon class="grey--text" small>mdi-select mdi-dark mdi-inactive</v-icon></span>
              <span v-if="item.YAZ == 1"> <v-icon class="orange--text" small>mdi-printer</v-icon> </span>
              <span v-else> <v-icon class="grey--text" small>mdi-select mdi-dark mdi-inactive</v-icon></span>
            </template>
            <template v-slot:[`item.FTACIK`]="{ item }">
              <span v-if="item.FTACIK == 1" class="green--text fw-bold">Kapalı</span>
              <span v-else>Açık</span>
            </template>
            <template v-slot:[`item._FTTUR`]="{ item }">
              <span class="grey--text fw-bold">{{ item._FTTUR }}</span>
            </template>
            <template v-slot:[`item.FTBELNO`]="{ item }">
              <span class="grey--text fw-bold">{{ item.FTBELNO }}</span>
            </template>
            <template v-slot:[`item.FTTOPLAM`]="{ item }">
              <span class="grey--text fw-bold">{{ vueNumberFormat(item.FTTOPLAM, {}) }}</span>
            </template>
            <template v-slot:[`item.FTKDVTUTAR`]="{ item }">
              <span class="grey--text fw-bold">{{ vueNumberFormat(item.FTKDVTUTAR, {}) }}</span>
            </template>
            <template v-slot:[`item.FTISKONTOTUTAR`]="{ item }">
              <span class="grey--text fw-bold">{{ vueNumberFormat(item.FTISKONTOTUTAR, {}) }}</span>
            </template>
            <template v-slot:[`item.FTTUTAR`]="{ item }">
              <span class="grey--text fw-bold">{{ vueNumberFormat(item.FTTUTAR, {}) }}</span>
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
            <template v-slot:[`body.append`]>
              <tr class="sticky-table-footer">
                <td v-text="''" />
                <td v-text="''" />
                <td v-text="''" />
                <td v-text="''" />
                <td v-text="''" />
                <td v-text="''" />
                <td v-text="'Toplamlar'" />
                <td v-text="total.RecordCount + ' Adet'" />
                <td v-text="total.matrah" />
                <td v-text="total.kdv" />
                <td v-text="total.indirim" />
                <td v-text="total.toplam" />
                <td v-text="''" />
                <td v-text="''" />
              </tr>
            </template>
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
          <v-menu class="LABEL-MENU" transition="scale-transition" bottom v-model="showRightMenu" :position-x="x" :position-y="y" absolute offset-y>
            <v-list>
              <v-list-item v-for="menuItem in rightMenuItems" @click="rightMenuMethod(menuItem.key)" :key="menuItem.title" dense>
                <v-list-item-content>
                  <v-list-item-title><v-icon left>mdi-view-list</v-icon>{{ menuItem.title }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-menu>
          <!-- </v-row> -->
          <!-- </v-col> -->
        </v-card-text>
      </v-card>
      <v-card-actions> </v-card-actions>
    </v-card>
  </div>
</template>

<script>
import moment from "moment";
import sensorApi from "@/api/sensorAPI";
import { mapGetters } from "vuex";
import { axiosConf3 } from "@/helpers/helpers";
import { sfatmast } from "./data";
import fatura from "Components/SelfFatura/fatura";
import faturadetayliste from "Components/SelfFatura/faturadetayliste";
import filtre from "Components/SelfUtils/filtre";

export default {
  name: "faturaliste",
  components: {
    fatura,
    faturadetayliste,
    filtre,
  },
  data() {
    return {
      yeniKayitMenuitems: [
        { id: 0, title: "Toptan Satış Faturası" },
        { id: 1, title: "Perakende Satış Faturası" },
        { id: 2, title: "Alış Faturası" },
        { id: 3, title: "Alış İade Faturası" },
        { id: 4, title: "Satış İade Faturası" },
        { id: 5, title: "Verilen Hizmet" },
        { id: 6, title: "Alınan Hizmet" },
      ],
      x: 0,
      y: 0,
      showRightMenu: false,
      rightMenuSecilenItem: {},
      rightMenuItems: [
        { title: "Yazdir", key: "yazdir" },
        { title: "Yaz=0", key: "yazdirsifir" },
        { title: "Faturayı Onayla", key: "onay" },
        { title: "Fatura Onay iptal Et", key: "onayiptal" },
        { title: "Fatura iptal Et", key: "faturaiptal" },
        { title: "Fatura iptal=0", key: "faturaiptalsifir" },
      ],
      total: {
        RecordCount: 0,
        matrah: 0,
        kdv: 0,
        indirim: 0,
        toplam: 0,
      },
      detayTotal: {},
      icons: ["mdi-facebook", "mdi-twitter", "mdi-linkedin", "mdi-instagram"],
      search: "",
      loadingTable: false,
      DetayDialog: false,
      dsListe: {
        DataRows: [],
        FieldNames: [],
      },
      componentMasterKey: 0,
      componentDetayKey: 0,
      MasterEdit: {
        masterid: null,
        editedIndex: -1,
        editedItem: {},
      },
      pagination: {
        page: 1,
        pagecount: 1,
        pageline: 15,
        pageoffset: 0,
        RecordCount: 0,
      },
      options: {},
      filtreDialog: false,
      mtFiltre: "",
    };
  },
  watch: {
    async options(val) {
      const { page, itemsPerPage } = val;
      console.log("page: " + page);
      this.pagination.pageoffset = itemsPerPage * (page - 1);
      this.pagination.pageline = itemsPerPage;
      this.listele();
    },
  },
  async created() {
    this.listele();
  },

  methods: {
    getFiltre() {
      this.mtFiltre = "mtfatura";
      this.filtreDialog = true;
      console.log("getFiltre");
    },
    set_filtre(item) {
      this.filtreDialog = false;
      this.listele();
      console.log(item + ": " + JSON.stringify(item, null, 2));
    },
    rowClick(item) {
      this.rightMenuSecilenItem = item;
    },
    async RightMenu(e, item) {
      this.rightMenuSecilenItem = item.item;
      //console.log("secilen: " + JSON.stringify(this.rightMenuSecilenItem, null, 2));
      e.preventDefault();
      this.x = e.clientX;
      this.y = e.clientY;
      this.showRightMenu = true;
    },

    async rightMenuMethod(key) {
      console.log(key);
      if (key == "yazdir") {
        this.rightMenuSecilenItem.YAZ = 1;
      }
      if (key == "yazdirsifir") {
        this.rightMenuSecilenItem.YAZ = 0;
      }
      if (key == "onay") {
        this.rightMenuSecilenItem.KILITLI = 2;
      }
      if (key == "onayiptal") {
        this.rightMenuSecilenItem.KILITLI = 0;
      }
      if (key == "faturaiptal") {
        this.rightMenuSecilenItem.FTIPTAL = 1;
      }
      if (key == "faturaiptalsifir") {
        this.rightMenuSecilenItem.FTIPTAL = 0;
      }
      let data = {
        SFATMASTID: this.rightMenuSecilenItem.SFATMASTID,
        YAZ: this.rightMenuSecilenItem.YAZ,
        KILITLI: this.rightMenuSecilenItem.KILITLI,
        FTIPTAL: this.rightMenuSecilenItem.FTIPTAL,
      };
      this.faturaDurumKaydet(data);
    },
    async faturaDurumKaydet(data) {
      await sensorApi
        .setData({
          table: "sfatmast",
          action: "U",
          crud: [
            {
              key: "SFATMASTID",
              value: data.SFATMASTID,
            },
          ],
          data: data,
        })
        .then((response) => {
          this.$appmesaj(true, response.mesaj);
        })
        .catch((error) => {
          this.$appmesaj(true, error.mesaj, "error");
        });
    },
    async closeDialog() {
      console.log("closeDialog");
    },

    async Sil(e, item) {
      // console.log(e);
      console.log(item);
      if (item.SFATMASTID != undefined && (await this.$root.$confirm("Satır Sil!", "Silinecek emin misiniz?", { color: "red" }))) {
        console.log("Evet");
      }
    },

    YeniKayit(item) {
      this.MasterEdit.masterid = -1;
      this.MasterEdit.editedIndex = -1;
      this.MasterEdit.editedItem = Object.assign({}, sfatmast); //default değere dön.
      this.MasterEdit.editedItem.FTTUR = item.id;
      this.MasterEdit.editedItem.FTTARIH = moment().format("Y-MM-DD"); //new Date();
      this.DetayDialog = true;
      this.rightMenuSecilenItem = {};
      this.showRightMenu = false;
    },

    Edit(item) {
      this.MasterEdit.masterid = item.SFATMASTID;
      this.MasterEdit.editedIndex = this.dsListe.DataRows.indexOf(item);
      this.MasterEdit.editedItem = JSON.parse(JSON.stringify(item));
      this.DetayDialog = true;
      this.rightMenuSecilenItem = {};
      this.showRightMenu = false;
    },

    //TODO: emit_sfatdet_edit
    sfatdet_edit() {
      this.MasterEdit.editedItem.GUNCELLEMEDATE = moment().format("Y-MM-DD HH:mm:ss");
    },

    sfatdet_total(event_value) {
      this.detayTotal = event_value;
      console.log("total: " + JSON.stringify(event_value, null, 2));
    },

    // TODO: fatura emit_sfatmast_kaydet AAA
    async sfatmast_kaydet(event_value) {
      let event = event_value.masterItem;

      let data_event = JSON.parse(JSON.stringify(event));
      //this.detayTotal
      data_event["FTTOPLAM"] = this.detayTotal.toplam;
      data_event["FTKDVTUTAR"] = this.detayTotal.kdv;
      data_event["FTISKONTOTUTAR"] = this.detayTotal.toplamiskonto;
      data_event["FTTUTAR"] = this.detayTotal.geneltoplam;

      //data_event["FTALTINDIRIM"] = this.detayTotal.;
      //data_event["FTALTINDIRIM2"] = this.detayTotal.;
      //data_event["FTALTINDIRIM3"] = this.detayTotal.;

      //! EDIT
      if (this.MasterEdit.editedIndex > -1) {
        //event["EditUser"] = this.getUser;
        //event["EditDate"] = moment().format("YMMDD HH:mm:ss");
        //event["EditComp"] = "WEB_HBYS";
        //console.log(moment().format("YMMDD HH:mm:ss"));

        // 0 kaydet, 1 yeni_satir
        if (event_value.type == "kaydet") {
          delete data_event["_CRISIM"];
          delete data_event["_CRKOD"];
          delete data_event["_FTTUR"];
          delete data_event["_FTALTINDIRIM"];
          delete data_event["_KALEMSAYISI"];
          await sensorApi
            .setData({
              table: "sfatmast",
              action: "U",
              crud: [
                {
                  key: "SFATMASTID",
                  value: event.SFATMASTID,
                },
              ],
              data: data_event,
            })
            .then((response) => {
              this.$appmesaj(true, response.mesaj);
              Object.assign(this.dsListe.DataRows[this.MasterEdit.editedIndex], event);
              this.DetayDialog = false;
            })
            .catch((error) => {
              this.$appmesaj(true, error.mesaj, "error");
            });
        }
        if (event_value.type == "yeni_satir") {
          //edit sfatmast, değişti yap, child e gönder, refresh et
          event.GUNCELLEMEDATE = moment().format("Y-MM-DD HH:mm:ss");
          this.MasterEdit.editedItem = JSON.parse(JSON.stringify(event));
          this.componentDetayKey += 1;
        }
        //
      }

      //! NEW
      if (this.MasterEdit.editedIndex == -1) {
        delete data_event["SFATMASTID"];
        delete data_event["_CRISIM"];
        delete data_event["_CRKOD"];
        delete data_event["_KALEMSAYISI"];
        await sensorApi
          .setData({
            table: "sfatmast",
            action: "I",
            crud: [],
            data: data_event,
          })
          .then((response) => {
            this.$appmesaj(true, response.mesaj);
            event.SFATMASTID = response.id;
            this.dsListe.DataRows.push(event);

            // 0 kaydet, 1 yeni_satir
            if (event_value.type == "kaydet") {
              this.DetayDialog = false;
            }
            if (event_value.type == "yeni_satir") {
              //new sfatmast, kayıt ekle, gelen id al, değişti yap, child e gönder, refresh et
              this.MasterEdit.masterid = event.SFATMASTID;
              this.MasterEdit.editedIndex = this.dsListe.DataRows.indexOf(event);

              event.GUNCELLEMEDATE = moment().format("Y-MM-DD HH:mm:ss");
              this.MasterEdit.editedItem = JSON.parse(JSON.stringify(event));
              this.componentMasterKey += 1;
            }
            //
          })
          .catch((error) => {
            this.$appmesaj(true, error.mesaj, "error");
          });
      }
    },

    async listele() {
      //debugLog("getSelfData.php: respFaturalar :  ", null, null, "SELF");
      if (this.loadingTable == true) {
        this.$notify({
          text: "Lütfen Seçimin yüklenmesini bekleyiniz!",
          type: "warn",
        });
        return;
      }
      this.showRightMenu = false;
      this.loadingTable = true;
      //const sqltext = axiosConf2(["K", "Id"], ["sfatmast", "0"], "sp_WebSelf_Fis");
      const sqltext = axiosConf3(["K", "Id", "_limit", "_offset"], ["sfatmast", "0", this.pagination.pageline, this.pagination.pageoffset], "sp_WebSelf_Fis");
      //console.log(": " + JSON.stringify(sqltext, null, 2));
      await sensorApi
        .getData({
          Action: "getSelfData.php",
          SelectYeni: sqltext,
          Type: "Liste",
          SetKey: "respFaturalar",
        })
        .then((response) => {
          this.$notify(response.mesaj);
          let layload = JSON.parse(JSON.stringify(this.getResponse["respFaturalar"]));
          //pagination
          this.pagination.pagecount = layload.PageCount;
          this.pagination.RecordCount = Number(layload.RecordCount);
          //toplamlar
          this.total.RecordCount = layload.RecordCount;
          this.total.matrah = layload.Total.matrah;
          this.total.kdv = layload.Total.kdv;
          this.total.indirim = layload.Total.indirim;
          this.total.toplam = layload.Total.toplam;

          let fields = [
            {
              text: "İşlem",
              value: "Sec",
              class: "blue lighten-2",
            },
            {
              text: "#",
              value: "SFATMASTID",
              class: "blue lighten-3",
              align: "right",
            },
            {
              text: "O/I/Y",
              value: "KILITLI",
              class: "blue lighten-2",
            },
            // {
            //   text: "iptal",
            //   value: "FTIPTAL",
            //   class: "blue lighten-2",
            //   width: 10,
            // },
            // {
            //   text: "Yaz",
            //   value: "YAZ",
            //   class: "blue lighten-2",
            //   width: 10,
            // },
            {
              text: "A/K",
              value: "FTACIK",
              class: "blue lighten-2",
            },
            {
              text: "Tarih",
              value: "FTTARIH",
              class: "blue lighten-3",
            },
            {
              text: "Belge No",
              value: "FTBELNO",
              class: "blue lighten-2",
            },
            {
              text: "Tür",
              value: "_FTTUR",
              class: "blue lighten-2",
            },
            {
              text: "Cari İsim",
              value: "_CRISIM",
              class: "blue lighten-2",
            },

            {
              text: "Matrah",
              value: "FTTOPLAM",
              class: "blue lighten-2",
              align: "right",
            },
            {
              text: "Kdv",
              value: "FTKDVTUTAR",
              class: "blue lighten-2",
              align: "right",
            },
            {
              text: "T.İndirim",
              value: "FTISKONTOTUTAR",
              class: "blue lighten-2",
              align: "right",
            },
            {
              text: "Toplam",
              value: "FTTUTAR",
              class: "blue lighten-2",
              align: "right",
            },
            {
              text: "Açıklama",
              value: "FTBILGI",
              class: "blue lighten-2",
            },
            {
              text: "Adet",
              value: "KALEMSAYISI",
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
        })
        .catch((error) => {
          this.$notify({
            text: error.mesaj,
            type: "error",
          });
        });
      this.loadingTable = false;
    },
    ListRowBackground(item) {
      console.log(this.MasterEdit.editedItem.SFATMASTID);
      if (item.SFATMASTID === this.MasterEdit.editedItem.SFATMASTID) {
        return "amber lighten-4 black--text";
      }
      if (item.SFATMASTID === this.rightMenuSecilenItem.SFATMASTID) {
        return "orange lighten-4 black--text";
      }
    },
  },

  computed: {
    ...mapGetters(["getResponse", "getUser", "darkMode", "dsGorevTanimlar"]),
    formTitle() {
      return this.MasterEdit.editedIndex === -1 ? "I" : "U";
    },
  },
};
</script>

<style scoped>
.sticky-table-footer td {
  font-weight: bold;
  position: sticky !important;
  bottom: 0 !important;
  background-color: #64b5f6;
  /* border-top: thin solid rgba(0, 0, 0, 0.52); */
  text-align: right !important;
  border: 2px solid rgb(255, 255, 255);
  /* margin-right: 1em; */
}
.sirano {
  font-size: 11px;
  color: rgb(8, 136, 187);
  /* border-radius: 25px; */
  /* background-color: #fa9e8d; */
  /* border: 2px solid rgb(71, 255, 255); */
}
</style>
