<template>
  <div>
    <div>
      <div>抢哪个！</div>
      <div v-for="(item) in detailList" :key="item.spu_id">
        <label @click="checkuuid(item.skus[0].uuid)" :for="item.spu_id">{{item.title}}<input class="checksku" :id="item.spu_id" :value="item.skus[0].uuid" type="checkbox"></label>
        <span>数量{{item.stock}}</span>
        <span>价格{{item.price}}</span>
      </div>
    </div>
    
    <button @click="getData">请求数据1</button>
    <button @click="startGo">开始抢购</button>
  </div>
</template>

<script setup>
import { ref,reactive } from 'vue'
import HelloWorld from './components/HelloWorld.vue'
import axios from 'axios'
const params = reactive({})
function ajax({ method = 'POST', url = '', params = {}, token = '',uid = '', host = "/api", headers = {} }) {
    method = method.toUpperCase()
    url = '/api' + url
    return new Promise((resolve, reject) => {
        let baseHeaders = {
            'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8',
        }
        let opt = {
            method: method,
            url: url,
            timeout: 1000 * 60,
            headers: Object.assign(baseHeaders, headers)
        } 
        if (method === 'POST'){
            opt.data = params
        } else if (method === 'GET') {
            opt.params = params
        }
        axios( opt )
        .then((res) => {
          resolve(res.data)
        })
        .catch((err) => {
            reject(err.data);
        })
    })
}
function getJson({ method = 'POST', url = '', params = {}, token = '',uid = '', host = "/api", headers = {} }) {
  return new Promise((resolve, reject)=>{
    ajax({ method, url, params, token, uid, host, headers }).then(data => resolve(data)).catch(err => reject(err))
  })
}
let detailList = ref([])
let orderDetail = reactive({
  orderDetail:{}
})
let flag = ref(false)

// 数据请求
function getData() {
  const param = new URLSearchParams();
  param.append("param", JSON.stringify({"source":"h5","v_seller_id":"","tabKey":"all"}));
  param.append("context", JSON.stringify({"appid":"wxbuyer","platform":"windows","token":"_EwWqqVIQBk1JWw5FqKw1gEMT7R59YJ-IULnty0tVJJj68lnfpLO-Lc6PSVU4pjwRDo-AQd50bFVaS9cQr4d8hknBxw2FwwtEP4Ezqbn2HYt7fYnt-7zaHDR2R6FIedmu2r_HJft-7PVeRUoy3Y9iDg8QzXtOTlV_C_XE0ApfqRta0d7KPkdeSZLxt0mC-upZgYbD_P0YdLfgDdYyzAVBQe_1vA_ArezIZwgO8WuuPPPZs8VylNmQWo2B53iM_12J9m35ScwJ",
  "duid":"1462145323","uss":"_EwWqqVIQBk1JWw5FqKw1gEMT7R59YJ-IULnty0tVJJj68lnfpLO-Lc6PSVU4pjwRDo-AQd50bFVaS9cQr4d8hknBxw2FwwtEP4Ezqbn2HYt7fYnt-7zaHDR2R6FIedmu2r_HJft-7PVeRUoy3Y9iDg8QzXtOTlV_C_XE0ApfqRta0d7KPkdeSZLxt0mC-upZgYbD_P0YdLfgDdYyzAVBQe_1vA_ArezIZwgO8WuuPPPZs8VylNmQWo2B53iM_12J9m35ScwJ","anonymousId":"4fb9c0e17590420095b7aa214cdb581432df5092","userID":"1462145323","wduserID":"1712976168","extEnv":{},"wx_source":"wdPlus","version":"0","wxappid":"wx4b74228baa15489a"}));
  getJson({
    host: '/api',
    url: '/vcart/getListCart/3.0',
    params:param,
    headers:{
      'Content-Type':'application/x-www-form-urlencoded;charset=UTF-8',
    }
  }).then((res) => {
    let cart = res.result.shops[0]
    orderD(cart)
  })
}

function orderD(cart) {
  let item_list = cart.partitions[0].itemList.map((item,index)=>{
    return {
      "item_id": item.itemId, 
      "quantity": item.count, 
      "price_type": "1", 
      "item_sku_id": item.skuId
    }
  })
  let orderParam = {
        "channel": "xiaochengxu", 
        "item_list": item_list, 
        "source_id": "5774937da7853cd3860c022c7fb13f84", 
        "wfr": "", 
        "self_address_id": "", 
        "address_id": "", 
        "buyer": {
            "eat_in_table_name": ""
        }
    }
    let orderContext = {
      "appid": "wxbuyer",
      "platform": "wx4b74228baa15489a",
      "token": "_EwWqqVIQBk1JWw5FqKw1gEMT7R59YJ-IULnty0tVJJj68lnfpLO-Lc6PSVU4pjwRDo-AQd50bFVaS9cQr4d8hknBxw2FwwtEP4Ezqbn2HYt7fYnt-7zaHDR2R6FIedmu2r_HJft-7PVeRUoy3Y9iDg8QzXtOTlV_C_XE0ApfqRta0d7KPkdeSZLxt0mC-upZgYbD_P0YdLfgDdYyzAVBQe_1vA_ArezIZwgO8WuuPPPZs8VylNmQWo2B53iM_12J9m35ScwJ",
      "duid": "1462145323",
      "uss": "_EwWqqVIQBk1JWw5FqKw1gEMT7R59YJ-IULnty0tVJJj68lnfpLO-Lc6PSVU4pjwRDo-AQd50bFVaS9cQr4d8hknBxw2FwwtEP4Ezqbn2HYt7fYnt-7zaHDR2R6FIedmu2r_HJft-7PVeRUoy3Y9iDg8QzXtOTlV_C_XE0ApfqRta0d7KPkdeSZLxt0mC-upZgYbD_P0YdLfgDdYyzAVBQe_1vA_ArezIZwgO8WuuPPPZs8VylNmQWo2B53iM_12J9m35ScwJ",
      "anonymousId": "4fb9c0e17590420095b7aa214cdb581432df5092",
      "userID": "1462145323",
      "wduserID": "1712976168",
      "version": "1.0.1",
      "miniProgramScene": 1001,
      "payEnv": {
        "environment": "WXAPP",
        "platform": "wx4b74228baa15489a",
        "from": "WXAPP"
        },
      "subChannel": "wdPlus",
      "wxEnvVersion": "release",
      "wxappid": "wx4b74228baa15489a"
    }
    const params1 = new URLSearchParams();
    params1.append("param", JSON.stringify(orderParam));
    params1.append("context", JSON.stringify(orderContext));

    getJson({
      host: '/api',
      url: '/vbuy/ConfirmOrder/1.0',
      params:params1,
      headers:{
        'Content-Type':'application/x-www-form-urlencoded;charset=UTF-8',
      }
    }).then((res)=>{
      orderDetail.orderDetail = res.result
      if (res.result && res.result.shop_list) {
        startGo()
        // for (let index = 0; index < 10; index++) {
        // }
      }else{
        orderD(cart)
      }
    })
}

function startGo() {
  let neworderDetail = orderDetail.orderDetail
  let shop_list = neworderDetail.shop_list.map((item,index)=>{
    let item_list = item.item_list.map((item,index)=>{
      return {
              "item_id": item.item_id,
              "quantity": item.quantity,
              "item_sku_id": item.item_sku_id,
              "ori_price": item.ori_price,
              "price": item.price,
              "extend": {},
              "price_type": 1,
              "discount_list": item.discount_list,
              "item_convey_info": item.item_convey_info
            }
    })
    let order_type = []
    let express_fee = []
    let express_type = []
    let discount_list = []
    if (item.shop.order_types.length) {
      order_type = item.shop.order_types[0].order_type
    }
    if (item.express_list.length) {
      express_fee = item.express_list[0].express_fee
    }
    if (item.express_types.type_list.length) {
      express_type = item.express_types.type_list[0].type
    }
    if (item.discount_list.length) {
      discount_list = item.discount_list.map((item,index)=>{
        if(item.option_list.length){
          return {
            id:item.option_list[0].id
          }
        }
        
      })
    }
    return {
            "shop_id": item.shop.shop_id,
            "f_shop_id": "",
            "sup_id": "",
            "item_list": item_list,
            "order_type": order_type,
            "ori_price": item.ori_total_price,
            "price": item.total_price,
            "express_fee": express_fee,
            "express_type": express_type,
            "discount_list": discount_list,
            "invalid_item_list": item.invalid_item_list
        }
  })
  
  
  let orderParam = {
    "channel": "xiaochengxu",
    "source_id": "5774937da7853cd3860c022c7fb13f84",
    "q_pv_id": neworderDetail.pvid,
    "biz_type": neworderDetail.biz_type,
    "buyer": {
        "buyer_id": "1462145323",
        "eat_in_table_name": "",
        "address_id": neworderDetail.buyer_address.address_id
    },
    "shop_list": shop_list,
    "deliver_type": 0,
    "is_no_ship_addr": neworderDetail.is_no_ship_addr,
    "total_pay_price": neworderDetail.total_pay_price,
    "total_vjifen": "",
    "wfr": "",
    "appid": "",
    "discount_list": [],
    "invalid_shop_list": neworderDetail.invalid_shop_list,
    "pay_type": 0
  }
  if (neworderDetail.custom_info) {
    let custom_info = neworderDetail.custom_info.map((item,index)=>{
      let custom = {}
      if (item.format == 'choice') {
        custom = {
                    "choice_list": item.choice_list,
                    "choice_type": item.choice_type,
                    "def_value": item.def_value,
                    "format": "choice",
                    "name": item.name,
                    "required": item.required,
                    "value": item.choice_list[0].name,
                    "self_choice": {
                        [item.choice_list[0].name]: true
                    },
                    "isCheck": true,
                    "serialId": 0,
                    "choice_value_list": [
                        item.choice_list[0].name
                    ]
                }
      } else {
        custom = {
                  "def_value": item.def_value,
                  "format": "text",
                  "name": item.name,
                  "required": 1,
                  "value": "111",
                  "isCheck": true,
                  "serialId": 0
                  }
      }
      return custom
    })
    orderParam.custom_info = custom_info
  }


  let orderContext = {
    "appid": "wxbuyer",
    "platform": "wx4b74228baa15489a",
    "token": "_EwWqqVIQBk1JWw5FqKw1gEMT7R59YJ-IULnty0tVJJj68lnfpLO-Lc6PSVU4pjwRDo-AQd50bFVaS9cQr4d8hknBxw2FwwtEP4Ezqbn2HYt7fYnt-7zaHDR2R6FIedmu2r_HJft-7PVeRUoy3Y9iDg8QzXtOTlV_C_XE0ApfqRta0d7KPkdeSZLxt0mC-upZgYbD_P0YdLfgDdYyzAVBQe_1vA_ArezIZwgO8WuuPPPZs8VylNmQWo2B53iM_12J9m35ScwJ",
    "duid": "1462145323",
    "uss": "_EwWqqVIQBk1JWw5FqKw1gEMT7R59YJ-IULnty0tVJJj68lnfpLO-Lc6PSVU4pjwRDo-AQd50bFVaS9cQr4d8hknBxw2FwwtEP4Ezqbn2HYt7fYnt-7zaHDR2R6FIedmu2r_HJft-7PVeRUoy3Y9iDg8QzXtOTlV_C_XE0ApfqRta0d7KPkdeSZLxt0mC-upZgYbD_P0YdLfgDdYyzAVBQe_1vA_ArezIZwgO8WuuPPPZs8VylNmQWo2B53iM_12J9m35ScwJ",
    "anonymousId": "4fb9c0e17590420095b7aa214cdb581432df5092",
    "userID": "1462145323",
    "wduserID": "1712976168",
    "version": "1.0.1",
    "miniProgramScene": 1001,
    "payEnv": {
      "environment": "WXAPP",
      "platform": "wx4b74228baa15489a",
      "from": "WXAPP"
      },
    "subChannel": "wdPlus",
    "wxEnvVersion": "release",
    "wxappid": "wx4b74228baa15489a"
  }
  const params2 = new URLSearchParams();
  params2.append("param", JSON.stringify(orderParam));
  params2.append("context", JSON.stringify(orderContext));
  getJson({
      host: '/api',
      url: '/vbuy/CreateOrder/1.0',
      params:params2,
      headers:{
        'Content-Type':'application/x-www-form-urlencoded;charset=UTF-8',
      }
    }).then((res)=>{
      if (res.status.message != 'OK') {
        startGo()
      }
    })
  
}

</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
