<template>
  <div class="ui">

    <div class="ui error message" v-if="error">
      <i v-on:click="error = false" class="close icon"></i>
      <div class="header">{{ $t('website.form.error.content') }}</div>
    </div>

    <div class="ui grid" v-if="this.$store.state.Website.$ == null">
      <div class="one wide column bottom aligned content">
        <router-link to="/config">
          <button class="ui icon button"><i class="setting icon"></i></button>
        </router-link>
      </div>
      <div class="fifteen wide column">
        <div class="ui form">
          <div class="field">
            <label>{{ $t('website.form.input.label') }} :</label>
            <input v-model="url" class="prompt" type="url" :placeholder="$t('website.form.input')">
          </div>
        </div>
      </div>
    </div>

    <div class="ui form" v-if="this.$store.state.Website.$ == null">
      <div class="field centered">
        <button v-on:click="update()" class="ui right labeled icon teal button"><i class="right arrow icon"></i> {{ $t('website.form.button') }} </button>
      </div>
    </div>

    <div class="ui form" v-if="this.$store.state.Website.$ != null">
      <div class="field centered">
        <button v-on:click="this.document.location.reload(true)" class="ui left labeled icon red button"><i class="undo icon"></i> {{ $t('website.form.button.reload') }} </button>
      </div>
    </div>
  </div>
</template>

<script>
  const recrawler = require('recrawler')

  export default {
    data () {
      return {
        url: '',
        error: false
      }
    },
    computed: {
      isLoading: function () {
        return this.$store.state.Website.isLoading
      }
    },
    methods: {
      update () {
        let isValide = true
        // Todo : Use regex

        if (isValide) {
          this.error = false
          this.$store.commit('UPDATE_LOADING', {
            show: true
          })

          this.$store.commit('UPDATE_WEBSITE', this.url)

          recrawler(this.url).then($ => {
            this.$store.commit('UPDATE_QUERY', $)
            this.$store.commit('UPDATE_LOADING', {
              show: false
            })
          }).catch(err => {
            console.log(err)
            this.$store.commit('UPDATE_LOADING', {
              show: false
            })
            this.$store.commit('UPDATE_ERROR', {
              show: true,
              title: err.message,
              text: err.code
            })
          })
        } else {
          this.error = true
        }
      }
    }
  }
</script>

<style scoped>
.centered {
  text-align: center!important
}
.ui.grid{
  padding-top: 10px;
  padding-bottom: 10px;
  vertical-align: top;
}
</style>
