<template>
  <el-menu
      default-active="3"

      mode="vertical"
      background-color="#304156"
      text-color="#bfcbd9"
      active-text-color="#409EFF"
      style="height:100%; border: 0px"
  >
    <!--
    Use of the components and class from element-ui to build the sidebar (el-submenu) and the icons from their gallery
    el-menu-item-group allows to have a group of items and a same defined function to manage the input of the group and
    to share info between components
    -->
    <el-submenu index="1">
      <template slot="title">
        <i class="el-icon-location"></i>
        <span>Layers</span>
      </template>

      <!--
      for the this first group of layers when the input change, it calls the manageAgrupation function with the
      checkboxGroupIndAgrupacion value (label value for each of them SC_barrios, SC_distritos and none)

      -->
      <el-menu-item-group>
        <el-radio-group v-model="checkboxGroupIndAgrupacion" @input=manageAgrupation()>
          <el-radio-button class='radio-group-menu' label="SC_barrios">Barrio</el-radio-button>
          <el-radio-button class='radio-group-menu' label="SC_distritos">Distrito</el-radio-button>
          <el-radio-button class='radio-group-menu' label="none">None</el-radio-button>
        </el-radio-group>
      </el-menu-item-group>
    </el-submenu>


    <el-submenu index="2">
      <template slot="title">
        <i class="el-icon-location"></i>
        <span>Indicators</span>
      </template>

      <!--
      for the this group of indicators when the input change, it calls the manageSelector function with the
      checkboxGroupInd value (label value for each of them SC_NDVI, SC_GSI,SC_GSD, SC_GSBS, SC_PI)
      -->


      <el-menu-item-group title="Green Space">
        <el-radio-group v-model="checkboxGroupInd">
          <el-menu-item index="1-1">
            <el-radio class='radio-group-menu' label="SC_NDVI" name="SC_NDVI" @change="manageSelector()"
                      style="margin-right: 112px;">
              NDVI
            </el-radio>
            <!-- popover to see a short definition of the index (use of components of element ui -> el-popover
            repeated for all the indexes
            -->
            <el-popover trigger="click"
                        placement="top"
                        width="400"
            >
              <div>
                <h3 class="popover-header">NDVI - Normalized Difference Vegetation Index</h3>
                <div>
                  It is a well-known spatial indicator to detect live green vegetation canopies.
                  It is calculated from multispectral data and provides information on the condition (growth or vigor)
                  of vegetated area
                </div>

              </div>
              <base-button slot="reference">
                <i class="el-icon-info"></i>
              </base-button>
            </el-popover>


          </el-menu-item>


          <el-menu-item index="1-2">
            <el-radio class='radio-group-menu' label="SC_GSI" name="SC_GSI" @change="manageSelector()"
                      style="margin-right: 87px;">
              GS Index
            </el-radio>

            <el-popover trigger="click"
                        placement="top"
                        width="400"
            >
              <div>
                <h3 class="popover-header">Green Space Index</h3>
                <p>
                  It is a measure of the provision of green space per person
                </p>

              </div>
              <base-button slot="reference">
                <i class="el-icon-info"></i>
              </base-button>
            </el-popover>

          </el-menu-item>
          <el-menu-item index="1-3">
            <el-radio class='radio-group-menu' label="SC_GSD" name="SC_GSD" @change="manageSelector()"
                      style="margin-right: 75px">
              GS Density
            </el-radio>

            <el-popover trigger="click"
                        placement="top"
                        width="400"
            >
              <div>
                <h3 class="popover-header">Green Space Density</h3>
                <div>
                  It is an indicator of the area occupied by green-spaces related to the total ground area. If
                  calculated using a predefined area unit, the indicator value can be mapped to get its spatial
                  distribution (diagnosis and design indicator)
                </div>

              </div>
              <base-button slot="reference">
                <i class="el-icon-info"></i>
              </base-button>
            </el-popover>

          </el-menu-item>

          <el-menu-item index="1-4">
            <el-radio class='radio-group-menu' label="SC_GSBS" name="SC_GSBS" @change="manageSelector()"
                      style="margin-right: 15px">
              GS Built-Space Ratio
            </el-radio>
            <el-popover trigger="click"
                        placement="top"
                        width="380"
            >
              <div>
                <h3 class="popover-header">Green Space Built-Space Ratio</h3>
                <div>
                  This indicator relates the area occupied by green-spaces and the built-up area
                </div>

              </div>
              <base-button slot="reference">
                <i class="el-icon-info"></i>
              </base-button>
            </el-popover>

          </el-menu-item>


          <el-menu-item index="1-5">
            <el-radio class='radio-group-menu' label="SC_PI" name="SC_PI" @change="manageSelector()"
                      style="margin-right: 28px">
              GS Proximity Index
            </el-radio>

            <el-popover trigger="click"
                        placement="top"
                        width="380"
            >
              <div>
                <h3 class="popover-header">Green Space Proximity Index</h3>
                <div>
                  It is an indicator of patch isolation and considers the size and proximity of all neighboring green
                  spaces
                </div>

              </div>
              <base-button slot="reference">
                <i class="el-icon-info"></i>
              </base-button>
            </el-popover>
          </el-menu-item>
        </el-radio-group>
      </el-menu-item-group>

    </el-submenu>

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

    /*Functions executed when the values from the menu are changed
      Use of this.$root.$emit with a name event and a send of the parameter
      In the specific components (MapBox.vue, BarChartDistBarr, BarChartHistogram) there is a event listener part to
      check for these events
    */

    manageSelector() {
      this.$root.$emit('change_layer', (this.checkboxGroupInd));
    },
    manageAgrupation() {
      console.log('hey')
      this.$root.$emit('change_layer_agrupation', (this.checkboxGroupIndAgrupacion));
    },
  }
}
</script>

<style scoped>

.radio-group-menu {
  color: #bfcbd9;
  font-weight: 300;
  font-size: 14px;
}

</style>