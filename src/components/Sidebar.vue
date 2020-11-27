<template>
  <el-menu
      default-active="3"
      @open="handleOpen"
      @close="handleClose"
      mode="vertical"
      background-color="#304156"
      text-color="#bfcbd9"
      active-text-color="#409EFF"
      style="height:100%"
  >
    <!--
    <el-menu-item index="1">
      <i class="el-icon-menu"></i> <span>Dashboard</span>
    </el-menu-item>
    -->
    <el-submenu index="3">
      <template slot="title">
        <i class="el-icon-location"></i>
        <span>Agrupación</span>
      </template>


      <el-menu-item-group>
        <el-radio-group v-model="checkboxGroupIndAgrupacion" @input=manageAgrupation()>
          <el-radio-button class='radio-group-menu' label="SC_barrios">Barrio</el-radio-button>
          <el-radio-button class='radio-group-menu' label="SC_distritos">Distrito</el-radio-button>
          <el-radio-button class='radio-group-menu' label="none">Nada</el-radio-button>
        </el-radio-group>
      </el-menu-item-group>
    </el-submenu>


    <el-submenu index="3">
      <template slot="title">
        <i class="el-icon-location"></i>
        <span>Layers</span>
      </template>


      <el-menu-item-group title="Green Space">
        <el-radio-group v-model="checkboxGroupInd">

          <el-menu-item index="1-1">
            <el-radio class='radio-group-menu' label="SC_NDVI" name="SC_NDVI" @change="manageSelector()">
              NDVI
            </el-radio>
          </el-menu-item>

          <el-menu-item index="1-2">
            <el-radio class='radio-group-menu' label="SC_GSI" name="SC_GSI" @change="manageSelector()">
              Green Space Index
            </el-radio>
          </el-menu-item>

          <el-menu-item index="1-3">
            <el-radio class='radio-group-menu' label="SC_GSD" name="SC_GSD" @change="manageSelector()">
              Green Space Density
            </el-radio>
          </el-menu-item>

          <el-menu-item index="1-4">
            <el-radio class='radio-group-menu' label="SC_GSBS" name="SC_GSBS" @change="manageSelector()">
              GS Built-Space Ratio
            </el-radio>
          </el-menu-item>

          <el-menu-item index="1-5">
            <el-radio class='radio-group-menu' label="SC_PI" name="SC_PI" @change="manageSelector()">
              GS Proximity Index
            </el-radio>
          </el-menu-item>

        </el-radio-group>
      </el-menu-item-group>

    </el-submenu>

    <!--
    <el-menu-item index="3">
      <i class="el-icon-s-data"></i> <span>Data</span>
    </el-menu-item>
    <el-menu-item index="4">
      <i class="el-icon-data-analysis"></i> <span>Charts</span>
    </el-menu-item>
    <el-menu-item index="5">
      <i class="el-icon-message"></i> <span>Contact</span>
    </el-menu-item>
    -->


  </el-menu>


</template>

<script>
export default {
  data() {
    return {
      checkboxGroupInd: 'SC_NDVI',
      checkboxGroupIndAgrupacion: 'none'
    }
  },
  methods: {
    handleOpen(key, keyPath) {
      console.log(key, keyPath);
    },
    handleClose(key, keyPath) {
      console.log(key, keyPath);
    },
    manageSelector() {
      this.$root.$emit('change_layer', (this.checkboxGroupInd));
    },
    manageAgrupation() {
      console.log('hey')
      this.$root.$emit('change_layer_agrupation', (this.checkboxGroupIndAgrupacion));
    }

  }
};
</script>

<style scoped>

.radio-group-menu {
  color: #bfcbd9;
  font-weight: 300;
  font-size: 14px;
}

</style>