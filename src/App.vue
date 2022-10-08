<template>
  <div>
    <button @click="isLoad ? openPopup() : loadComponent()"> open popup</button>
    <InvitePopup
        v-if="isLoad"
        ref="popup"
        @mounted="openPopup"
    />

    <div v-if="data" style="max-width: 560px">
      <h3>SUCCESS !!!</h3>
      <h5>Your invite data</h5>
      <hr>
      <table>
        <tr>
          <th>Name</th>
          <th>Mail</th>
          <th>Role</th>
        </tr>
        <tr v-for="item in data.inviteData">
          <td>{{item.name}}</td>
          <td>{{item.mail}}</td>
          <td>{{item.role}}</td>
        </tr>
      </table>

      <h5>Your Personal message</h5>
      <hr>
      <p>{{data.personalMessage}}</p>
    </div>
  </div>
</template>

<script>
import { defineAsyncComponent } from 'vue';

export default {
  name: 'App',
  components: {
    InvitePopup: defineAsyncComponent(() => import('@/components/invite-others/InvitePopup.vue')),
  },

  data () {
    return {
      isLoad: false,
      data: null
    };
  },

  methods: {
    loadComponent() {
      this.isLoad = true
    },

    async openPopup () {
      this.data = await this.$refs.popup.openPopup();
    },
  },
};
</script>

<style lang="scss">
@import "src/assets/scss/shared/var";
@import "src/assets/scss/shared/common";
</style>


