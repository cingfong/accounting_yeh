<template>
  <div id="app">
    <div>
      <b-form>
        <b-form-input
          v-for="inputItem in inputList"
          :key="inputItem.model"
          id="input-1"
          v-model="formData[inputItem.model]"
          type="text"
          :placeholder="inputItem.text"
          :disabled="inputItem.disabled"
          required
        />
        <b-button @click="onSubmit" variant="primary">下一項</b-button>
      </b-form>
      {{ formList }}
    </div>
  </div>
</template>

<script>
export default {
  // name: "App",
  data() {
    return {
      inputList: [
        { text: "輸入項目", model: "name" },
        { text: "輸入規格", model: "criterion" },
        { text: "輸入數量", model: "number" },
        { text: "輸入單位", model: "unit" },
        { text: "輸入單價", model: "price" },
        { text: "輸入小計", model: "total", disabled: true },
        { text: "輸入備註", model: "remark" },
      ],
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
  methods: {
    onSubmit() {
      this.formList.push({ ...this.formData });
      Object.keys(this.formData).forEach((key) => (this.formData[key] = ""));
    },
  },
};
</script>

<style></style>
