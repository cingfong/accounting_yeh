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
        <b-button @click="onSubmit" variant="primary" class="mt-2"
          >下一項</b-button
        >
      </b-form>
      {{ formList }}
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
        { text: "輸入小計", model: "total", disabled: true },
        { text: "輸入備註", model: "remark" },
      ],
      formTitle: "",
      formData: {
        name: "",
        criterion: "",
        number: "",
        unit: "",
        price: "",
        total: "",
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
.form-title-input {
}
.form-wrap {
  width: 350px;
}
</style>
