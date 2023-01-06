<template>
  <div id="app" class="w-full d-flex justify-content-center">
    <div
      v-if="titleFormShow"
      class="form-title-input-wrap d-flex justify-content-center align-items-center"
    >
      <b-card
        class="p-0"
        bg-variant="dark"
        text-variant="white"
        title="請輸入抬頭名稱"
      >
        <b-input v-model="formTitle" class="form-title-input my-3"></b-input>
        <b-button class="mt-2" variant="primary" @click="titleFormShow = false">
          確定
        </b-button>
      </b-card>
    </div>
    <div class="form-wrap">
      <label for="inputPassword5" class="form-label px-2">請款單</label>
      <b-form>
        <b-form-input
          v-for="inputItem in inputList"
          :key="inputItem.model"
          class="mt-2"
          id="input-1"
          v-model="formData[inputItem.model]"
          type="text"
          :placeholder="inputItem.text"
          :disabled="inputItem.disabled"
          required
        />
        <b-form-input
          id="input-1"
          v-model="itemTotal"
          type="text"
          placeholder="小計"
          disabled
          required
        />
        <b-button @click="onSubmit" variant="primary">下一項</b-button>
      </b-form>
    </div>
    <div>
      <table class="table">
        <thead>
          <tr>
            <th scope="col">施工項目</th>
            <th scope="col">規格</th>
            <th scope="col">數量</th>
            <th scope="col">單位</th>
            <th scope="col">單價</th>
            <th scope="col">小計</th>
            <th scope="col">備註</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(formItem, index) in formList" :key="index">
            <th scope="row" v-for="(item, key) in formItem" :key="`item${key}`">
              <button
                v-if="key === 'name'"
                type="button"
                class="btn btn-warning"
              >
                編輯
              </button>
              {{ item }}
            </th>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      titleFormShow: true,
      inputList: [
        { text: "輸入項目", model: "name" },
        { text: "輸入規格", model: "criterion" },
        { text: "輸入數量", model: "number" },
        { text: "輸入單位", model: "unit" },
        { text: "輸入單價", model: "price" },
        // { text: "輸入小計", model: "total", disabled: true },
        { text: "輸入備註", model: "remark" },
      ],
      formTitle: "",
      formData: {
        name: "",
        criterion: "",
        number: "",
        unit: "",
        price: "",
        // total: "",
        remark: "",
      },
      formList: [],
    };
  },
  computed: {
    itemTotal() {
      return this.formData.price * this.formData.number;
    },
  },
  mounted() {},
  methods: {
    onSubmit() {
      this.formList.push({ ...this.formData });
      Object.keys(this.formData).forEach((key) => (this.formData[key] = ""));
    },
  },
};
</script>

<style>
.form-title-input-wrap {
  position: fixed;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.6);
}
.card-body {
  width: 350px;
}
/* .form-title-input {
} */
.form-wrap {
  width: 350px;
}
</style>
