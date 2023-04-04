<template>
  <div class="app">
    <div class="header">
      <img src="DRS_horizontal_fullcolor.svg" width="400" height="180" />
      <h1>Dr. Squatch Soap Bundles</h1>
      <ScentFilter @updateScents="scentSearch($event.data)" :scents="scents" />
    </div>
    <div class="body">
      <div v-for="bundle in filterScents" :key="bundle.handle">
        <SoapBundle :bundle="bundle" />
      </div>
    </div>
  </div>
</template>

<script>
import ScentFilter from '@/components/ScentFilter.vue'
import SoapBundle from '@/components/SoapBundle.vue'
import '@/assets/base.css'
export default {
  name: 'App',
  components: {
    ScentFilter,
    SoapBundle
  },
  data() {
    return {
      bundles: [],
      scents: [],
      bundleTitle: '',
      selectedScents: []
    }
  },
  computed: {
    filterScents() {
      if (!this.selectedScents.length) {
        return this.bundles
      }
      return this.bundles.filter((bundle) =>
        bundle.scent.some((scent) => this.selectedScents.includes(scent))
      )
    }
  },
  methods: {
    scentSearch(array) {
      this.selectedScents = array
    },

    async getBundleData() {
      try {
        const response = await fetch(
          `https://ae3t7l1i79.execute-api.us-east-1.amazonaws.com/bundles`
        )
        const data = await response.json()
        this.bundles = data
        return data
      } catch (error) {
        console.log(error)
      }
    },
    async getProductData(product) {
      try {
        const response = await fetch(
          `https://ae3t7l1i79.execute-api.us-east-1.amazonaws.com/product/${product}`
        )
        const data = await response.json()
        return data
      } catch (error) {
        console.log(error)
      }
    },
    async bundleList() {
      const data = await this.getBundleData()
      this.bundles = data
    },

    async includedProductData(product) {
      const data = await this.getProductData(product)
      const includedProductData = {
        title: data.title,
        price: data.price,
        scent: data.scent_profile
      }
      return includedProductData
    },
    formatIncludedProductList(bundle) {
      const productHashMap = {}
      let includedProdStr = ''
      bundle.titles.forEach((title) => {
        if (!productHashMap[title]) {
          productHashMap[title] = 1
        } else {
          productHashMap[title] += 1
        }
      })
      for (let i = 0; i < Object.keys(productHashMap).length; i++) {
        if (i === Object.keys(productHashMap).length - 1) {
          includedProdStr += `${Object.keys(productHashMap)[i]} x ${
            Object.values(productHashMap)[i]
          }`
        } else {
          includedProdStr += `${Object.keys(productHashMap)[i]} x ${
            Object.values(productHashMap)[i]
          }, `
        }
      }
      return includedProdStr
    },
    getScents() {}
  },
  async created() {
    await this.bundleList()
    this.bundles.forEach((bundle) => {
      bundle.products_included.forEach(async (product) => {
        const response = await this.includedProductData(product)
        if (bundle.scent) {
          response.scent.forEach((scent) => {
            if (!this.scents.includes(scent)) {
              this.scents.push(scent)
            }
            if (!bundle.scent.includes(scent)) {
              bundle.scent.push(scent)
            }
          })
        } else {
          bundle.scent = response.scent
        }
        if (!bundle.titles) {
          bundle.titles = [response.title]
        } else {
          bundle.titles.push(response.title)
        }
        bundle.includedItems = this.formatIncludedProductList(bundle)
      })
    })
  }
}
</script>

<style>
.header {
  position: flex;
  top: 0px;
  margin-top: 20px;
  margin-left: 20px;
}

.body {
  box-sizing: border-box;
  display: grid;
  grid-template-columns: 1fr 1fr;
}
</style>
