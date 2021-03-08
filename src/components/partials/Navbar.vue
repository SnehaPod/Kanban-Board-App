<template>
  <b-navbar
    toggleable="sm"
    type="light"
    variant="light"
    class="bg-transparent navbar-border"
  >
    <b-navbar-toggle target="nav-text-collapse"></b-navbar-toggle>

    <b-navbar-brand class="brand-title ml-2"> <b> ToDo It? </b> </b-navbar-brand>

    <b-collapse id="nav-text-collapse" is-nav>
      <!-- Right aligned elements -->
      <b-navbar-nav class="ml-auto">
        <!-- Plus icon -->
        <b-icon
          class="icons border-secondary mx-3 my-2"
          variant="secondary"
          scale="1.25"
          icon="plus-circle"
        ></b-icon>
        <!--  -->

        <!-- Warning icon -->
        <b-icon
          class="icons border-secondary mx-3 my-2"
          variant="secondary"
          scale="1.25"
          icon="exclamation-circle"
        ></b-icon>
        <!--  -->

        <!-- Bell icon -->
        <b-icon
          class="icons border-secondary mx-3 my-2"
          variant="secondary"
          scale="1.25"
          icon="bell"
        ></b-icon>
        <!--  -->

        <!-- Avatar -->
        <b-icon
          class="icons border-secondary mx-3 my-2"
          variant="secondary"
          scale="1.25"
          icon="person"
        ></b-icon>
        <!--  -->
      </b-navbar-nav>
      <!--  -->
    </b-collapse>
  </b-navbar>
</template>

<script>
export default {
  name: "NavbarComponent",
};
</script>

<style scoped>
.navbar-border {
  border-bottom: 1px solid #e9ecef;
  padding: 1px;
  box-shadow: 1px 1px #e9ecef;
}
.brand-title {
  font-family: "Quicksand", sans-serif;
  font-weight: 700;
}
.icons {
  cursor: pointer;
}
</style>