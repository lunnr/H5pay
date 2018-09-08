<template>
    <div>
        <div class="goodList">
            <div class="goodGroup of" v-for="good in goods" :key="good.id">
                <div class="good left"><img :src='good.img' alt=""></div>
                <div class="description left">{{good.description}}</div>
                <div class="amount left">{{good.amount | amount}}</div>
                <div class="price left">{{good.price | price}}</div>
            </div>
        </div>
        <div class="totalPrice">
            <span>合计：{{totalPrice | price}}</span>
        </div>
        <div class="paySelect ">
            <p>请选择支付方式：</p>
            <ul>
                <li v-for="pay in payMethods" :key="pay.id" class="of">
                    <img :src="pay.img" class="left" alt="">
                    <span class="right" :class="{active:params.payType === pay.name}" @click="setPayType(pay.name)"></span>
                </li>
            </ul>
        </div>
        <div class="pay">确认支付</div>
    </div>
</template>

<script>
import alipay from "../assets/alipay.jpg";
import wechat from "../assets/wechat.jpg";
export default {
  name: "Home",
  data() {
    return {
      goods: [
        {
          id: 1,
          description: "快乐水",
          img: require("../assets/kuole.png"),
          amount: 1,
          price: 3
        },
        {
          id: 2,
          description: "快乐水",
          img: require("../assets/kuole.png"),
          amount: 1,
          price: 3
        }
      ],
      payMethods: [
        {
          id: 1,
          name: "微信支付",
          img: alipay
        },
        {
          id: 2,
          name: "支付宝支付",
          img: wechat
        }
      ],
      params: {
        payType: null
      }
    };
  },

  components: {},

  computed: {
    totalPrice() {
      let total = 0;
      this.goods.forEach(val => {
        total += val.price;
      });
      return total;
    }
  },

  mounted() {
    let merchanCode = this.$route.query.merchantCode;
    let data = `merchantCode=${merchantCode}`;
  },

  methods: {
    setPayType(name) {
      this.params.payType = name;
    },
    startPay(money) {
      let orderData = this.getOrder(money);
      this.$axios({
          method:'POST',
          data:orderData,
          url:'/scanpay/unified'
      }).then( res => {

      })
    },
    //生成订单
    getOrder(money) {
      let orderNum = this.getOrderNumber();
      // 验证码
      let veriCode = "";
      for (let i = 0; i < 4; i++) {
        veriCode += Math.floor(Math.random() * 10);
      }
      return {
        version: "2.3.2",
        signType: "SHA256",
        charset: "utf-8",
        orderNum: orderNum,
        txndir: "Q",
        busicd: "JSZF",
        inscd: "",
        mchntid: "",
        terminalid: "10000001",
        txamt: this.amtFormat(money)
      };
    },
    // 生成订单号
    getOrderNumber() {
      let dateStr = this.formatDate(new Date());
      let veriCode = "";
      for (let i = 0; i < 5; i++) {
        veriCode += Math.floor(Math.random() * 10).toString();
      }
      return dateStr + veriCode;
    },
    // 格式化金额
    formatMoney(money) {
      money = Number(money)
        .toFixed(2)
        .toString();
      while (money.lenght < 12) {
        money = "0" + money;
      }
      return money;
    },
    // 格式化日期
    formatDate(date) {
      if (!(date instanceof Date)) {
        return "";
      }
      let year = date.getFullYear();
      let month =
        date.getMonth() > 8
          ? (date.getMonth() + 1).toString()
          : "0" + (date.getMonth() + 1).toString();
      let day =
        date.getDate() > 9
          ? date.getDate().toString()
          : "0" + date.getDate().toString();
      let hour =
        date.getHours() > 9
          ? date.getHours().toString()
          : "0" + date.getHours().toString();
      let minute =
        date.getminutes() > 9
          ? date.getminutes().toString()
          : "0" + date.getminutes().toString();
      let second =
        date.second() > 9
          ? date.second().toString()
          : "0" + date.second().toString();
      return year + month + day + hour + minute + second;
    }
  },

  filters: {
    price(val) {
      return val + "￥";
    },
    amount(val) {
      return val + "个";
    }
  }
};
</script>
<style lang='scss' scoped>
@import "../style/mixin.scss";
.goodList {
  .goodGroup {
    border: 1px solid #ccc;
    border-bottom: none;
    &:nth-last-of-type(1) {
      border-bottom: 1px solid #ccc;
    }
    line-height: 40px;
    .good {
      width: 40%;
      line-height: 0;
      img {
        width: 40px;
      }
    }
    .description {
      width: 20%;
    }
    .amount {
      width: 20%;
      text-align: center;
    }
    .price {
      width: 20%;
      text-align: center;
    }
  }
}
.totalPrice {
  line-height: 30px;
  margin: 6px 0 0 0;
  display: flex;
  flex-direction: row-reverse;
  span {
    background: red;
    color: #fff;
    border-radius: 3px;
    box-sizing: border-box;
    padding: 2px 6px;
  }
}
.paySelect {
  line-height: 40px;
  li {
    line-height: 40px;
    img {
      @include wh(50px, 40px);
      line-height: 0;
    }
    span {
      display: inline-block;
      margin: 10px 5px 0 0;
      @include wh(20px, 20px);
      border-radius: 50%;
      background: #ccc;
      &.active {
        background: #000;
      }
    }
  }
}
.pay {
  line-height: 35px;
  text-align: center;
  background: green;
  color: #fff;
  border-radius: 6px;
  margin: 10px 0 0 0;
}
</style>