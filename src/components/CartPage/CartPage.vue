<template>
  <div class="page">
    <div class="header">
      <li>
        <div class="logo">Genius IceCream Cart</div>
      </li>
      <li><button class="bag"><router-link to="/your-profile"><img src="../assets/Sample_User_Icon.png"
              class="bag-img"></router-link>
        </button></li>
    </div>
    <div class="cart">
      <li>
        <CartItem :cart_data="CART" v-for="(item, index) in CART" :key="item.name" :cart_item_data="item"
          @deleteFromCart="deleteFromCart(index)" />
      </li>

    </div>

    <li>
      <div class="row">
        <div class="total">Total price: {{ cartTotalCost }}</div>
        <button class="order" @click="makeNewOrder()">Make order</button>
      </div>
    </li>


  </div>
</template>

<script>
import CartItem from './CartItem.vue'
import { mapGetters, mapActions } from 'vuex'

export default {
  name: 'CartPage',
  components: { CartItem },
  computed: {
    ...mapGetters([
      'PRODUCTS',
      'CART'
    ]),
    cartTotalCost() {
      let result = []
      if (this.CART.length) {
        for (let item of this.CART) {
          result.push(item.cost * item.number)
        }
        result = result.reduce(function (sum, el) {
          return sum + el;
        })
        return result.toFixed(1);
      }
      return 0;
    }
  },
  props: {
    cart_data: {
      type: Array,
      default() {
        return []
      }
    }
  },
  methods: {
    ...mapActions([
      'DELETE_FROM_CART',
      'DELETE_ALL',
      'GET_PRODUCTS_FROM_API',
      'ADD_TO_CART_FROM_LOCAL'
    ]),
    deleteFromCart(index) {
      this.DELETE_FROM_CART(index)
    },
    makeNewOrder() {
      if (!this.CART.length) {
        alert('There is no goods in the cart!');
      }
      else if (!localStorage.getItem("user")) {
        alert('Please write some information about yourself first!')
      }
      else {
        let checkOrder = 1;
        while (localStorage.getItem(checkOrder)) {
          checkOrder += 1;
        }
        const order = {
          date: new Date(),
          cost: 0
        }
        let res = Object.create(order);
        res.date = (' ' + new Date().getFullYear() + '-' + (new Date().getMonth() + 1) + '-' + new Date().getDate() + ' ' +
          new Date().getHours() + ":" + new Date().getMinutes() + ":" + new Date().getSeconds());
        for (let index of this.CART) {
          res.cost += index.number * index.cost;
        }
        res.cost = res.cost.toFixed(1);

        localStorage.setItem(checkOrder, JSON.stringify(res));

        for (let product = 0; product < this.CART.length; product++) {
          this.DELETE_ALL(product);
        }

        this.CART.splice(this.CART[0], this.CART.length);
      }
    }
  },
  mounted() {
    this.GET_PRODUCTS_FROM_API(),
    this.ADD_TO_CART_FROM_LOCAL()
  }
}
</script>

<style>
li {
  display: inline-block;
  margin: 15px 40px;
}

.row {
  margin: auto;
  display: flex;
}

.total {
  font-size: 23px;
  color: #490a11;
  margin-top: 8px;
  margin-right: 30px;
}

.order {
  border-radius: 5px;
  border: none;
  background-color: #d6d8c8;
  margin-top: 8px;
  justify-items: center;
  max-height: 107px;
  width: 180px;
  cursor: pointer;
  font-size: 23px;
  color: #490a11;
  font-family: SomeCoolFont2;
}

.order:active {
  background-color: rgba(219, 88, 23, 0.329)
}

.bag {
  padding-top: 35px;
  float: right;
  height: 65px;
  width: 65px;
  cursor: pointer;
  border: none;
  background-color: #d4d5cf;
}

.bag-img {
  height: 40px;
  width: 40px;
}

.header {
  display: flex;
  justify-content: center;
  padding-bottom: 50px;
  background-color: #d4d5cf;
}

.logo {
  padding-top: 30px;
  float: left;
  text-align: center;
  margin-right: 400px;
  font-family: SomeCoolFont;
  font-weight: 800;
  font-size: 55px;
  color: #74166a;
}

.cart {
  margin-top: 60px;
}
</style>