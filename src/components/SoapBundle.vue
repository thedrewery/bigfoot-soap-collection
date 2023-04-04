<template>
  <div class="bundle-item">
    <img class="product" :src="this.bundle.imageSrc" width="350" height="350" />
    <h2>{{ this.bundle.title }}</h2>
    <div v-if="this.originalPrice === this.bundle.price">
      <span :style="{ fontWeight: 'bold', color: 'green' }">${{ this.bundle.price / 100 }}</span>
    </div>
    <div v-else>
      <span :style="{ fontWeight: 'bold', color: 'green' }">${{ this.bundle.price / 100}}</span>
      <span :style="{ textDecoration: 'line-through', color: 'grey', margin: '2px' }"
        >${{ this.originalPrice /100 }}</span
      >
    </div>
    <div class="scents">
      <span v-for="(scent, index) in this.bundle.scent" :key="index" :class="scent">
        {{ scent.toUpperCase() }}
      </span>
    </div>
    <span :style="{ fontWeight: 'bold' }">Included:</span>
    <p>{{ this.bundle.includedItems }}</p>
  </div>
</template>

<script>
export default {
    name: 'SoapBundle',
    props: {
        bundle: {},
    },
    data() {
        return {
            originalPrice: 0
        }
    },
    created() {
        this.calcOriginalPrice()
    },
    methods: {
        async getProductData(product) {
            try {
                const response = await fetch(`https://ae3t7l1i79.execute-api.us-east-1.amazonaws.com/product/${product}`)
                const data = await response.json()
                return data;
            } catch (error) {
                console.log(error)
            }
    },
        async includedProductData(product) {
            const data = await this.getProductData(product);
            const includedProductData = {
                title: data.title,
                price: data.price,
                scent: data.scent_profile
            }
            return includedProductData;
    },
        async calcOriginalPrice() {
            this.bundle.products_included.forEach(async (product) => {
                const response = await this.includedProductData(product)
                if (this.originalPrice === 0) {
                    this.originalPrice = response.price;
                } else {
                    this.originalPrice += response.price;
                }
            })
        }
    }
}
</script>

<style>
.bundle-item {
  margin: 2rem;
  padding: 2rem;
  border: 1px solid #afaeb0;
  border-radius: 8px;
}

.scents {
  color: white;
}

span.woodsy {
  background-color: #165834;
  border-radius: 9px;
  padding: 2px;
  margin: 0, 5px;
}
span.fresh {
  background-color: #006fd6;
  border-radius: 9px;
  padding: 2px;
  margin: 3px;
}
span.citrus {
  background-color: #de7c00;
  border-radius: 9px;
  padding: 2px;
  margin: 3px;
}
span.herbal {
  background-color: #5a3714;
  border-radius: 9px;
  padding: 2px;
  margin: 3px;
}
span.rich {
  background-color: #e0a17e;
  border-radius: 9px;
  padding: 2px;
  margin: 3px;
}
span.spiced {
  background-color: #c10000;
  border-radius: 9px;
  padding: 2px;
  margin: 3px;
}
</style>
