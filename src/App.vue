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
    <div class="form-wrap mt-2">
      <b-form>
        <caption
          class="form-label px-2 d-flex justify-content-center align-items-center"
        >
          <b-icon icon="pencil-square" @click="titleFormShow = true" />
          <span style="padding-left: 10px">{{ formTitle }}請款單</span>
        </caption>
        <b-form-input
          v-for="inputItem in inputList"
          :key="inputItem.model"
          class="mt-2"
          id="input-1"
          v-model="formData[inputItem.model]"
          :type="inputItem.type"
          :placeholder="inputItem.text"
          :disabled="inputItem.disabled"
          required
        />
        <p class="mt-2">小計 : {{ itemTotal }}</p>
        <div class="d-flex justify-content-between">
          <b-button @click="addFormList" variant="primary">下一項</b-button>
          <b-button
            v-show="!checkString(formEditIndex)"
            variant="danger"
            @click="deleteFormList"
          >
            刪除
          </b-button>
          <b-button
            v-show="checkString(formEditIndex)"
            variant="danger"
            @click="formDataRemove"
          >
            清空
          </b-button>
        </div>
      </b-form>
    </div>
    <div>
      <div class="table-wrap mt-4">
        <table class="table table-DOM" id="pdf-wrap">
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
              <td
                scope="row"
                v-for="(item, key) in formItem"
                :key="`item${key}`"
              >
                <b-icon
                  v-if="key === 'name'"
                  icon="pencil-square"
                  @click="formEdit(index)"
                />
                {{ item }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="form-total-wrap d-flex align-items-end flex-column">
        <p class="mb-0 mt-2">
          合計 : <span>{{ listTotal }}</span>
        </p>
        <p class="mb-0 mt-2">
          稅金 : <span>{{ listTax }}</span>
        </p>
        <p class="mb-0 mt-2">
          總計 : <span>{{ listTaxIncluded }}</span>
        </p>
      </div>
    </div>
    <b-button class="mt-5" @click="createPDF" variant="success">
      生成pdf
    </b-button>
  </div>
</template>
<script>
// const { jsPDF } = require("jspdf");
// const { autoTable } = require("jspdf-autotable");
import jsPDF from "jspdf";
import autoTable from "jspdf-autotable";
import "./ch-normal.js";
// import "jspdf-autotable";
export default {
  data() {
    return {
      titleFormShow: false,
      inputList: [
        { text: "輸入項目", model: "name", type: "text" },
        { text: "輸入規格", model: "criterion", type: "text" },
        { text: "輸入數量", model: "number", type: "number" },
        { text: "輸入單位", model: "unit", type: "text" },
        { text: "輸入單價", model: "price", type: "number" },
        { text: "輸入備註", model: "remark", type: "text" },
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
    listTotal() {
      return this.formList.reduce(
        (total, item) => (total += item.itemTotal),
        0
      );
    },
    listTax() {
      return +(this.listTotal * 0.05).toFixed(2);
    },
    listTaxIncluded() {
      return this.listTotal + this.listTax;
    },
  },
  mounted() {},
  methods: {
    checkString(val) {
      return typeof val === "string";
    },
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
    createPDF() {
      const vm = this;
      if (!vm.formList.length) return;
      const doc = new jsPDF("p");
      doc.setFont("ch");
      doc.setLineWidth(2);
      let width = doc.internal.pageSize.getWidth();
      doc.text("業績明細報表", width / 2, 10, { align: "center" });
      doc.text("日期: ", width - 5, 10, { align: "right" });
      autoTable(doc, {
        html: "#pdf-wrap",
        styles: { font: "ch" },
      });
      // doc.save(`${vm.formTitle}請款單.pdf`);
      window.open(doc.output("bloburl"));
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
  height: 5px;
}
.table-wrap::-webkit-scrollbar-track-piece {
  background-color: #eee;
}

.table-wrap::-webkit-scrollbar-thumb {
  background-color: gray;
  border-radius: 15px;
}

.table-DOM {
  width: 750px !important;
}
.form-total-wrap {
  width: 350px;
}
.form-total-wrap p {
  width: 180px;
}
.form-total-wrap p span {
  width: calc(100% - 45px);
  display: inline-block;
  text-align: right;
}
</style>
