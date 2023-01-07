<template>
  <div id="app" class="w-full d-flex align-items-center flex-column">
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
      <label
        class="form-label px-2 d-flex justify-content-center align-items-center"
      >
        <b-icon icon="pencil-square" @click="titleFormShow = true" />
        <span style="padding-left: 10px">{{ formTitle }}請款單</span>
      </label>
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
        <p class="mt-2">小計 : {{ itemTotal }}</p>
        <div class="d-flex justify-content-between">
          <b-button @click="addFormList" variant="primary">下一項</b-button>
          <b-button
            v-show="typeof formEditIndex !== 'string'"
            @click="deleteFormList"
            variant="danger"
            >刪除</b-button
          >
        </div>
      </b-form>
    </div>
    <div class="table-wrap mt-4">
      <table class="table table-DOM">
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
              <b-icon
                v-if="key === 'name'"
                icon="pencil-square"
                @click="formEdit(index)"
              />
              {{ item }}
            </th>
          </tr>
        </tbody>
      </table>
    </div>
    <b-button class="mt-5" variant="success">確定</b-button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      titleFormShow: false,
      inputList: [
        { text: "輸入項目", model: "name" },
        { text: "輸入規格", model: "criterion" },
        { text: "輸入數量", model: "number" },
        { text: "輸入單位", model: "unit" },
        { text: "輸入單價", model: "price" },
        { text: "輸入備註", model: "remark" },
      ],
      formTitle: "",
      formData: {
        name: "",
        criterion: "",
        number: "",
        unit: "",
        price: "",
        itemTotal: "",
        remark: "",
      },
      formList: [],
      formEditIndex: "",
    };
  },
  computed: {
    itemTotal() {
      return this.formData.price * this.formData.number;
    },
  },
  mounted() {},
  methods: {
    formEdit(index) {
      const vm = this;
      vm.formEditIndex = index;
      vm.formData = vm.formList[index];
    },
    addFormList() {
      const vm = this;
      vm.formData.itemTotal = vm.itemTotal;
      // 新增情況
      if (typeof vm.formEditIndex === "string") {
        vm.formList.push({ ...vm.formData });
      } else {
        vm.formList[vm.formEditIndex] = { ...vm.formData };
        vm.formEditIndex = "";
      }
      vm.formDataRemove();
    },
    formDataRemove() {
      Object.keys(this.formData).forEach((key) => (this.formData[key] = ""));
    },
    deleteFormList() {
      this.formList.splice(this.formEditIndex, 1);
      this.formEditIndex = "";
      this.formDataRemove();
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
.form-wrap {
  width: 350px;
}
.table-wrap {
  width: 350px;
  min-height: 150px;
  overflow: auto;
}
.table-wrap::-webkit-scrollbar {
  height: 3px;
}
.table-wrap::-webkit-scrollbar-track-piece {
  background-color: #eee;
}

.table-wrap::-webkit-scrollbar-thumb {
  background-color: gray;
}

.table-DOM {
  width: 750px !important;
}
</style>
