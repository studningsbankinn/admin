<template>
  <div>
    <Hero
      :title="title"
      :subtitle="subtitle"
      :x="true"
    />
    <section class="box">
      <form @submit.prevent>
        <Notification
          :message="message"
          :success="success"
          :error="error"
        />
        <div class="columns">
          <Input
            v-model="user.name"
            :disabled="working"
            label="Fullt nafn"
            class="column is-12"
          />
        </div>

        <div
          v-if="!isEdit"
          class="columns"
        >
          <Input
            v-model="user.username"
            :disabled="working"
            label="Netfang"
            class="column is-6"
          />

          <Input
            v-model="user.password"
            :disabled="working"
            type="password"
            label="Lykilorð"
            class="column is-6"
          />
        </div>

        <div class="columns">
          <Select
            v-model="user.placeId"
            :disabled="working"
            :options="places"
            label="Má bara fá gögn um stað"
            class="column is-6"
          />

          <Select
            v-model="user.placeCategoryId"
            :disabled="working"
            :options="placeCategories"
            label="Má bara fá gögn um flokk"
            class="column is-6"
          />
        </div>
        <div
          v-if="isEdit"
          class="columns"
        >
          <Input
            v-model="user.token"
            :disabled="true"
            label="Aðgangslykill"
            class="column is-12"
          />
        </div>

        <div class="columns">
          <Button
            v-if="isEdit"
            :disabled="working"
            type="danger"
            label="Eyða"
            class="column is-6"
            @click="del()"
          />

          <Button
            :disabled="working"
            label="Vista"
            class="column is-6 has-text-right"
            @click="save()"
          />
        </div>
      </form>
    </section>
  </div>
</template>

<script>
import makeAPI from '../api'
import Hero from '../_components/hero'
import Notification from '../_components/notification'
import Input from '../_components/input'
import Select from '../_components/select'
import Button from '../_components/button.vue'
import EditMixin from '../_mixins/edit'

export default {
  name: 'UsersEdit',
  components: {
    Input,
    Select,
    Hero,
    Notification,
    Button
  },
  mixins: [EditMixin],
  data () {
    return {
      usersApi: {},
      user: {},
      placesApi: {},
      places: [],
      placeCategoriesApi: {},
      placeCategories: []
    }
  },
  computed: {
    title () {
      return this.user.name ? this.user.name : 'Notendur'
    },
    subtitle () {
      return this.isEdit ? 'Breyttu skráningu notanda.' : 'Stofnaðu nýjan notanda og gefðu honum viðeigandi aðgang'
    }
  },
  created () {
    this.working = true
    this.usersApi = makeAPI('users')
    this.placesApi = makeAPI('places')
    this.placeCategoriesApi = makeAPI('placecategories')
    const id = this.$route.params.id

    this.usersApi
      .getSingle(id)
      .then(user => {
        this.user = user
        this.working = false
      })
      .catch(() => {
        this.error = true
        this.message = 'Villa kom upp. Notandi fannst ekki.'
      })

    this.placesApi
      .getAll()
      .then(places => {
        this.places = places.map(place => ({
          value: place.id,
          text: place.name
        }))
      })
      .catch(e => {
        this.error = true
        this.message = 'Villa kom upp við að sækja staði'
      })

    this.placeCategoriesApi
      .getAll()
      .then(categories => {
        this.placeCategories = categories.map(place => ({
          value: place.id,
          text: place.name
        }))
      })
      .catch(e => {
        this.error = true
        this.message = 'Villa kom upp við að sækja staði'
      })
  },
  methods: {
    save () {
      this.working = true
      this.success = false
      this.error = false

      this.usersApi
        .upsert(this.user)
        .then(user => {
          if (user.id) {
            this.success = true
            this.message = 'Uppfærsla tókst'

            setTimeout(() => {
              this.$router.push({
                name: 'ListUsers'
              })
            }, 2500)
          } else {
            this.error = true
            this.message = 'Tókst ekki að vista'
          }
        })
        .catch(() => {
          this.error = true
          this.message = 'Villa kom upp við aðgerð'
        })
        .finally(() => {
          this.working = false
        })
    },
    del () {
      this.working = true
      this.success = false
      this.error = false

      this.usersApi
        .delete(this.user)
        .then(user => {
          if (user.id) {
            this.success = true
            this.message = 'Uppfærsla tókst'
            setTimeout(() => {
              this.$router.push({
                name: 'ListUsers'
              })
            }, 2500)
          } else {
            this.error = true
            this.message = 'Tókst ekki að eyða'
          }
        })
        .catch(() => {
          this.error = true
          this.message = 'Villa kom upp við aðgerð'
        })
        .finally(() => {
          this.working = false
        })
    }
  }
}
</script>
