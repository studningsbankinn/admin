<template>
  <div>
    <Hero
      title="Spurningalistar"
      subtitle="Stofnaðu nýjan lista eða breyttu eldri skráningu."
    />
    <section class="box">
      <table class="table is-fullwidth">
        <thead>
          <tr>
            <th            
              class="has-text-centered has-text-grey-light"
              width="9%"
            >
              #
            </th>
            <th width="89%">
              Heiti
            </th>
            <th
              width="1%"
              class="has-text-right"
            />
            <th
              width="1%"
              class="has-text-right"
            >
              <button
                class="button is-small is-info"
                @click="add()"
              >
                <span class="icon">
                  <i class="fas fa-plus" />
                </span>
              </button>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="category in placeCategories"
            :key="category.id"
          >
            <td class="has-text-centered has-text-grey-light">{{ category.id }}</td>
            <td>{{ category.name }}</td>
            <td class="has-text-right">
              <button
                class="button is-small is-warning"
                @click="editQuestionnaires(category.id)"
              >
                <span class="icon">
                  <i class="fas fa-list" />
                </span>
              </button>
            </td>
            <td class="has-text-right">
              <button
                class="button is-small is-warning"
                @click="edit(category.id)"
              >
                <span class="icon">
                  <i class="fas fa-pen" />
                </span>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>

<script>
import makeAPI from '../api'
import Hero from '../_components/hero'

export default {
  name: 'PlaceCategoriesList',
  components: {
    Hero
  },
  data () {
    return {
      placeCategories: []
    }
  },
  created () {
    const placeCategoriesApi = makeAPI('placecategories')

    placeCategoriesApi
      .getAll()
      .then(categories => {
        this.placeCategories = categories
      })
  },
  methods: {
    add () {
      this.$router.push({
        name: 'EditPlaceCategories',
        params: { id: 'add' }
      })
    },
    edit (id) {
      this.$router.push({
        name: 'EditPlaceCategories',
        params: { id }
      })
    },
    editQuestionnaires (id) {
      this.$router.push({
        name: 'EditQuestionnaires',
        params: { id }
      })
    }
  }
}
</script>
