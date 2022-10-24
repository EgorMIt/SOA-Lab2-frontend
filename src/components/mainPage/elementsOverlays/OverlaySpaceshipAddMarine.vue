<template>
  <v-form v-model="valid" lazy-validation ref="form">
    <!-- Оверлей загрузки солдата на корабль -->
    <v-card
        color="#F7FAFC"
    >
      <v-card-text class="font-weight-medium" style="font-size: 15pt; ">
        <div style="color: black; text-align: center; margin-bottom: 5%; font-size: 25px; line-height: 1">
          <br>Загрузить космодесантника на корабль
        </div>
      </v-card-text>

      <v-card-text class="font-weight-medium" style="font-size: 15pt; ">
        <v-icon left style="display: inline-block">
          {{ icons.mdiHumanScooter }}
        </v-icon>
        <v-select
            v-model="ChooseSpacemarine"
            :items="Spacemarines"
            :item-text="'name'"
            :item-value="'id'"
            :rules="rules.clearFieldValid"
            label="Выберете космодесантника"
            solo
            required
            style="border-radius: 5px; display: inline-block; width: 90%"
        ></v-select>

        <v-icon left style="display: inline-block">
          {{ icons.mdiShipWheel }}
        </v-icon>
        <v-select
            v-model="ChooseSpaceship"
            :items="Spaceships"
            :item-text="'name'"
            :item-value="'id'"
            :rules="rules.clearFieldValid"
            label="Выберете космический корабль"
            solo
            required
            style="border-radius: 5px; display: inline-block; width: 90%"
        ></v-select>

        <v-btn style="margin-left: 23%; margin-bottom: 2% ;margin-top: 20px"
               color=#0E1117
               outlined
               :loading="loadingRemove"
               @click="addMarineToSpaceship"
        >
          <v-icon left>
            {{ icons.mdiFormatVerticalAlignBottom }}
          </v-icon>
          Загрузить на корабль
        </v-btn>
      </v-card-text>
      <v-divider style="margin-bottom: 5%"></v-divider>

      <v-btn style="margin-left: 39%; margin-bottom: 5%"
             color=#0E1117
             outlined
             @click="closeDialog"
      >
        Закрыть
      </v-btn>
    </v-card>
  </v-form>
</template>

<script>
import axios from "axios";
import {mdiFormatVerticalAlignBottom, mdiShipWheel, mdiHumanScooter} from "@mdi/js";

export default {
  name: "OverlaySpaceshipAddMarine",

  data: () => ({
    icons: {
      mdiFormatVerticalAlignBottom,
      mdiShipWheel,
      mdiHumanScooter
    },
    absolute: true,
    valid: true,

    ChooseSpaceship: '',
    ChooseSpacemarine: '',
    Spaceships: [],
    Spacemarines: [],
    loadingRemove: false,

    rules: {
      clearFieldValid: [
        v => !!v || 'Поле не может быть пустым'
      ],
      numberValid: [
        v => !!v || 'Поле не может быть пустым',
        v => !!/^\d*$/.test(v) || 'Допустимы только числа',
      ],
    },
  }),
  methods: {
    closeDialog() {
      this.$emit('updateParent', {
        dialog: false,
      })
    },

    getListOfSpaceships() {
      let str = "/spaceships"
      axios.create(
      ).get(str)
          .then(resp => {
            console.log(resp.data)
            this.Spaceships = resp.data.items
          })
    },

    getListOfSpacemarines() {
      let str = "/spacemarines"
      axios.create(
      ).get(str)
          .then(resp => {
            console.log(resp.data)
            this.Spacemarines = resp.data.items
          })
    },

    async addMarineToSpaceship() {
      if (this.$refs.form.validate()) {
        this.loadingRemove = true
        let str = "/spaceships/" + this.ChooseSpaceship + "/load/" + this.ChooseSpacemarine
        axios.create(
        ).post(str)
            .then(resp => {
              console.log(resp.data)
            })
        await new Promise(resolve => setTimeout(resolve, this.awaitTimer))
        this.updateOverlay()
        this.loadingRemove = false
      }
    }

  },
  beforeMount() {
    this.getListOfSpaceships()
    this.getListOfSpacemarines()
    const array = [];
    array.push({
      id: 1,
      name: "Name1"
    })
    array.push({
      id: 2,
      name: "Name2"
    })
    this.Spaceships = array
    this.Spacemarines = array
  },
}
</script>

<style scoped>

</style>