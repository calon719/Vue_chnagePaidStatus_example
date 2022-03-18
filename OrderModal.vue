<template>
  <div
    class="modal fade"
    id="orderModal"
    tabindex="-1"
    role="dialog"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
    ref="modal"
  >
    <div class="modal-dialog modal-xl" role="document">
      <div class="modal-content border-0">
        <div class="modal-header bg-dark text-white">
          <h5 class="modal-title" id="exampleModalLabel">
            <span>訂單細節</span>
          </h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <div class="row">
            <div class="col-md-4">
              <h3>用戶資料</h3>
              <table class="table">
                <tbody v-if="tempOrderObj.user">
                  <tr>
                    <th style="width: 100px">姓名</th>
                    <td>{{ tempOrderObj.user.name }}</td>
                  </tr>
                  <tr>
                    <th>Email</th>
                    <td>{{ tempOrderObj.user.email }}</td>
                  </tr>
                  <tr>
                    <th>電話</th>
                    <td>{{ tempOrderObj.user.tel }}</td>
                  </tr>
                  <tr>
                    <th>地址</th>
                    <td>{{ tempOrderObj.user.address }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div class="col-md-8">
              <h3>訂單細節</h3>
              <table class="table">
                <tbody>
                  <tr>
                    <th style="width: 100px">訂單編號</th>
                    <td>{{ tempOrderObj.id }}</td>
                  </tr>
                  <tr>
                    <th>下單時間</th>
                    <td>{{ $filters.date(tempOrderObj.create_at) }}</td>
                  </tr>
                  <tr>
                    <th>付款時間</th>
                    <td>
                      <span v-if="tempOrderObj.paid_date">
                        {{ $filters.date(tempOrderObj.paid_date) }}
                      </span>
                      <span v-else>時間不正確</span>
                    </td>
                  </tr>
                  <tr>
                    <th>付款狀態</th>
                    <td>
                      <strong v-if="tempOrderObj.is_paid" class="text-success"
                        >已付款</strong
                      >
                      <span v-else class="text-muted">尚未付款</span>
                    </td>
                  </tr>
                  <tr>
                    <th>總金額</th>
                    <td>
                      {{ $filters.currency(tempOrderObj.total) }}
                    </td>
                  </tr>
                </tbody>
              </table>
              <h3>選購商品</h3>
              <table class="table">
                <thead>
                  <tr></tr>
                </thead>
                <tbody>
                  <tr v-for="item in tempOrderObj.products" :key="item.id">
                    <th>
                      {{ item.product.title }}
                    </th>
                    <td>{{ item.qty }} / {{ item.product.unit }}</td>
                    <td class="text-end">
                      {{ $filters.currency(item.final_total) }}
                    </td>
                  </tr>
                </tbody>
              </table>
              <div class="d-flex justify-content-end">
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="checkbox"
                    value=""
                    id="flexCheckDefault"
                    v-model="tempOrderObj.is_paid"
                  />
                  <label class="form-check-label" for="flexCheckDefault">
                    <span v-if="tempOrderObj.is_paid">已付款</span>
                    <span v-else>未付款</span>
                  </label>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button
            type="button"
            class="btn btn-outline-secondary"
            data-bs-dismiss="modal"
          >
            取消
          </button>
          <button type="button" class="btn btn-primary" @click="updatePaidData()">
            修改付款狀態
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import modalMixin from '../mixins/modalMixin.js'

export default {
  props: ['order', 'orderModal'],
  mixins: [modalMixin],
  data () {
    return {
      status: {},
      tempOrderObj: this.order
    }
  },
  watch: {
    order () {
      this.tempOrderObj = JSON.parse(JSON.stringify(this.order))
    }
  },
  methods: {
    // 修改訂單資料
    updatePaidData (order = this.tempOrderObj) {
      this.$http
        .put(
          `${process.env.VUE_APP_API}/v2/api/${process.env.VUE_APP_PATH}/admin/order/${order.id}`,
          { data: order }
        )
        .then((res) => {
          console.log(res)
          this.orderModal.hide()
          this.$emit('get-order-data')
        })
        .catch((err) => {
          console.dir(err.data)
        })
    }
  }
}
</script>
