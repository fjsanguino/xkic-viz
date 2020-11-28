<template>
  <el-menu
      default-active="3"
      @open="handleOpen"
      @close="handleClose"
      mode="vertical"
      background-color="#304156"
      text-color="#bfcbd9"
      active-text-color="#409EFF"
      style="height:100%; border: 0px"
  >
    <!--
    <el-menu-item index="1">
      <i class="el-icon-menu"></i> <span>Dashboard</span>
    </el-menu-item>
    -->
    <el-submenu index="1">
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


    <el-submenu index="2">
      <template slot="title">
        <i class="el-icon-location"></i>
        <span>Layers</span>
      </template>


      <el-menu-item-group title="Green Space">
        <el-radio-group v-model="checkboxGroupInd">
          <el-menu-item index="1-1">
            <el-radio class='radio-group-menu' label="SC_NDVI" name="SC_NDVI" @change="manageSelector()"
                      style="margin-right: 112px;">
              NDVI
            </el-radio>
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

    <el-menu-item index="4">
      <el-select v-model="distritosSelector" placeholder="Select" @change="manageSelecDist()">
        <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
            :name = "item.value"
        >
        </el-option>
      </el-select>
    </el-menu-item>

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
      options: [
        {
          value: '01',
          label: 'Centro',
        },
        {
          value: '02',
          label: 'Arganzuela',
        },
        {
          value: '03',
          label: 'Retiro',
        },
        {
          value: '04',
          label: 'Salamanca',
        },
        {
          value: '05',
          label: 'Chamartín',
        },
        {
          value: '06',
          label: 'Tetuán',
        },
        {
          value: '07',
          label: 'Chamberi',
        },
        {
          value: '08',
          label: 'Fuencarral-El Pardo',
        },
        {
          value: '09',
          label: 'Moncloa-Aravaca',
        },
        {
          value: '10',
          label: 'Latina',
        },
        {
          value: '11',
          label: 'Carabanchel',
        },
        {
          value: '12',
          label: 'Usera',
        },

        {
          value: '13',
          label: 'Puente de Vallecas',
        },
        {
          value: '14',
          label: 'Moratalaz',
        },
        {
          value: '15',
          label: 'Ciudad Lineal',
        },
        {
          value: '16',
          label: 'Hortaleza',
        },
        {
          value: '17',
          label: 'Villaverde',
        },
        {
          value: '18',
          label: 'Villa de Vallecas',
        },
        {
          value: '19',
          label: 'Vicálvaro',
        },
        {
          value: '20',
          label: 'San-Blas Canillejas',
        },
        {
          value: '21',
          label: 'Barajas',
        }



      ],
      distritosSelector: '',
      checkboxGroupInd: 'SC_NDVI',
      checkboxGroupIndAgrupacion: 'none'
    }
  },
  methods: {

    manageSelector() {
      this.$root.$emit('change_layer', (this.checkboxGroupInd));
    },
    manageAgrupation() {
      console.log('hey')
      this.$root.$emit('change_layer_agrupation', (this.checkboxGroupIndAgrupacion));
    },
    manageSelecDist(){
      this.$root.$emit('change_selector', (this.distritosSelector));
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