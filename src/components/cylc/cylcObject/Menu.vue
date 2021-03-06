<!--
Copyright (C) NIWA & British Crown (Met Office) & Contributors.

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
-->

<template>
  <div class="c-mutation">
    <!-- dropdown menu -->
    <v-menu
      v-model="showMenu"
      :position-x="x"
      :position-y="y"
      v-on:show-mutations-menu="showMutationsMenu"
      offset-y
      :disabled="!interactive"
    >
      <v-list dense>
        <v-subheader>{{id}}</v-subheader>
        <v-list-item-group color="primary">
          <v-list-item two-line
            v-for="mutation in mutations"
            :key="mutation.name"
            @click="callMutationFromContext(mutation)"
          >
            <v-list-item-icon>
              <div style="font-size:0.5rem;">
                <v-icon medium>{{ mutation._icon }}</v-icon>
              </div>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>
                <span>{{mutation._title}}</span>
              </v-list-item-title>
              <v-list-item-subtitle>
                <span>{{mutation._shortDescription}}</span>
              </v-list-item-subtitle>
            </v-list-item-content>
            <!-- This is how to add buttons for standard operations later -->
            <!--<v-list-item-action>-->
            <!--  <v-btn small rounded color="primary" dark>Foo</v-btn>-->
            <!--</v-list-item-action>-->
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-menu>
  </div>
</template>

<script>
import {
  getMutationArgsFromTokens,
  mutate
} from '@/utils/aotf'

export default {
  name: 'CylcObjectMenu',

  props: {
    interactive: {
      type: Boolean,
      required: false,
      default: true
    }
  },

  data () {
    return {
      showMenu: false,
      id: '',
      mutations: [],
      tokens: [],
      x: 0,
      y: 0
    }
  },

  mounted () {
    this.$eventBus.on('show-mutations-menu', this.showMutationsMenu)
  },

  beforeDestroy () {
    this.$eventBus.off('show-mutations-menu', this.showMutationsMenu)
  },

  methods: {
    /* Call a mutation using only the tokens for args. */
    callMutationFromContext (mutation) {
      // eslint-disable-next-line no-console
      console.debug(`mutation: ${mutation._title} ${this.id}`)
      const promise = mutate(
        mutation,
        getMutationArgsFromTokens(mutation, this.tokens),
        this.$workflowService.apolloClient
      )
      promise.then((status, ret) => {
        // eslint-disable-next-line no-console
        console.debug(status, ret)
      })
    },
    showMutationsMenu ({ id, tokens, mutations, event }) {
      this.id = id
      this.tokens = tokens
      this.mutations = mutations
      this.x = event.clientX
      this.y = event.clientY
      this.$nextTick(() => {
        this.showMenu = true
      })
    }
  }
}
</script>
