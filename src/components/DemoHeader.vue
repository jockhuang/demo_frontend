<template>
  <q-header elevated>
    <q-toolbar>
      <q-btn class="q-mr-sm" dense flat icon="menu" round/>
      <q-avatar>
        <img src="@/assets/logo.svg">
      </q-avatar>

      <q-toolbar-title>Demo</q-toolbar-title>

      <q-btn dense flat icon="whatshot" round/>
    </q-toolbar>

    <q-tabs v-model="activeLink" align="left">
      <q-route-tab exact label="Home" name="Home" to="/"/>
      <q-route-tab exact label="Product" name="Product" to="/product"/>
      <q-route-tab exact label="Subscriptions" name="Subscriptions" to="/subscriptions"/>
    </q-tabs>
  </q-header>

</template>

<script type='text/babel'>
export default {
  mounted: function () {
    console.log("router");
    console.log(this.$route.path);
    this.activeLink = window.location.pathname;
  },
  data() {
    return {
      activeLink: "/",
      menuRoutes: [
        {name: 'Home', path: '/'},
        {name: 'Product', path: '/product'},
        {name: 'Subscriptions', path: '/subscriptions'}
      ],
    };
  },
};
</script>