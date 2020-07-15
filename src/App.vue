<template>
  <div id="app">
    <div class="layout-row cell-outter">
      <div class="cell title">
        <div>入住時間</div>
      </div>
      <div class="cell content">
        <div slot name="cellModify">
          <select v-model="globalEntry.checkin_mon" style="width:50px; height:20px;">
            <option v-for="index in 12" v-bind:value="index" v-bind:key="index">{{ index }}</option>
          </select>月
          <select v-model="globalEntry.checkin_day" style="width:50px; height:20px;">
            <option v-for="index in 31" v-bind:value="index" v-bind:key="index">{{ index }}</option>
          </select>日
        </div>
      </div>
    </div>
    <div class="layout-row cell-outter">
      <div class="cell title">
        <div>退房時間</div>
      </div>
      <div class="cell content">
        <div slot name="cellModify">
          <select v-model="globalEntry.checkout_mon" style="width:50px; height:20px;">
            <option v-for="index in 12" v-bind:value="index" v-bind:key="index">{{ index }}</option>
          </select>月
          <select v-model="globalEntry.checkout_day" style="width:50px; height:20px;">
            <option v-for="index in 31" v-bind:value="index" v-bind:key="index">{{ index }}</option>
          </select>日
        </div>
      </div>
    </div>
    <div class="layout-row cell-outter">
      <div class="cell title">
        <div>有無烤肉</div>
      </div>
      <div class="cell content">
        <div slot name="cellModify">
          <input type="radio" v-model="globalEntry.BBQ" value="1">
          <label>有</label>
          <input type="radio" v-model="globalEntry.BBQ" value="0">
          <label>沒有</label>
        </div>
      </div>
    </div>
    <div class="layout-row cell-outter">
      <div class="cell title">
        <div>總價</div>
      </div>
      <div class="cell content">
        <div>{{totalPrice()}}</div>
      </div>
    </div>
    <div class="layout-row cell-outter">
      <div class="cell title">
        <div>打卡總價</div>
      </div>
      <div class="cell content">
        <div>{{totalDidcountPrice()}}</div>
      </div>
    </div>
    <div>
      <button
        type="button"
        class="btn"
        v-on:close="showCopyModal=false"
        v-on:click="showCopyModal=true"
      >複製文字</button>
    </div>
    <button
      type="button"
      class="btn"
      v-on:click="showCreateModal=true"
      v-bind:class="{disabled: checkItem.length!==0}"
    >
      <span>新增日期</span>
    </button>
    <button
      type="button"
      class="btn"
      v-on:click="deleteEntry"
      v-bind:disabled="checkItem.length==0"
    >
      <span>刪除日期</span>
    </button>
    <apply-button
      v-bind:lockApply="modfiedItemIndexs.length==0"
      v-bind:lockCancel="checkItem.length==0"
      v-on:apply="applyEntries()"
      v-on:cancel="read()"
    ></apply-button>
    <modal-dialog
      v-if="showCreateModal"
      v-bind:isShowModal="showCreateModal"
      v-on:notifyShowModal="showCreateModal=false"
      v-on:createEntry="createList"
    ></modal-dialog>
    <copy-dialog
      v-if="showCopyModal"
      v-bind:isShowModal="showCopyModal"
      v-bind:globalEntry="globalEntry"
      v-bind:daliyEntry="list"
      v-on:close="showCopyModal=false"
      v-on:copyText="copyText"
    ></copy-dialog>
    <control-table
      v-on:isChecked="selectEntry"
      v-bind:dbChanges="modfiedItemIndexs"
      v-bind:nums="list.length"
      ref="ControlTable"
    >
      <template slot="head">
        <th style="width:15%;">入住時間</th>
        <th style="width:10%;">人數</th>
        <th style="width:10%;">加床</th>
        <th style="width:20%;">總價</th>
        <th style="width:20%;">總價(含加床)</th>
        <th style="width:10%;">打卡折數</th>
        <th style="width:20%;">打卡價</th>
      </template>
      <template slot-scope="props" slot="body">
        <td>{{oldList[props.index].checkin_mon}}/{{oldList[props.index].checkin_day}}</td>
        <td>
          <div v-if="!list[props.index].isSelected">{{oldList[props.index].peopleNum}}</div>
          <select v-if="list[props.index].isSelected" v-model.number="list[props.index].peopleNum">
            <option v-for="index in 16" v-bind:value="index" v-bind:key="index">{{ index }}</option>
          </select>
        </td>
        <td>
          <div v-if="!list[props.index].isSelected">{{oldList[props.index].addBedNum}}</div>
          <select v-if="list[props.index].isSelected" v-model.number="list[props.index].addBedNum">
            <option v-for="index in 10" v-bind:value="index-1" v-bind:key="index">{{ index-1 }}</option>
          </select>
        </td>
        <td>
          <div v-if="!list[props.index].isSelected">{{oldList[props.index].price}}</div>
          <input
            v-if="list[props.index].isSelected"
            type="text"
            v-model.number="list[props.index].price"
          >
        </td>
        <td>
          <span>{{list[props.index].totalPrice}}</span>
        </td>
        <td>
          <div
            v-if="!list[props.index].isSelected"
          >{{(oldList[props.index].discount%2==0)?(oldList[props.index].discount/10):(oldList[props.index].discount)}}</div>
          <select v-model.number="list[props.index].discount" v-if="list[props.index].isSelected">
            <option value="100">不打</option>
            <option
              v-for="index in 10"
              v-bind:value="100-(index*5)"
              v-bind:key="index"
            >{{ (index%2 === 0)?(100-(index*5))/10:(100-(index*5)) }}</option>
          </select>
        </td>
        <td>
          <span>{{list[props.index].discountPrice}}</span>
        </td>
      </template>
    </control-table>
  </div>
</template>

<script>
import ControlTable from "./components/ControlTable";
import ApplyButton from "./components/ApplyButton";
import ModalDialog from "./components/ModalDialog";
import CopyDialog from "./components/CopyDialog";

const bbqPrice = 1200;
const addBedPrice = 600;
const BOOKING_API = "https://y0uoe.sse.codesandbox.io/";

export default {
  name: "app",
  data() {
    return {
      showCreateModal: false,
      showCopyModal: false,
      list: [],
      oldList: [],
      globalEntry: {},
      link: ""
    };
  },
  computed: {
    modfiedItemIndexs: function() {
      return _.flatMap(this.list, (entry, i) =>
        entry.isSelected && !_.isMatch(entry, this.oldList[i]) ? [i] : []
      );
    },
    nextModifiedItem: function() {
      return this.list
        .filter((el, index) => {
          return el.isSelected && !_.isMatch(el, this.oldList[index]);
        })
        .shift();
    },
    checkItem: function() {
      return this.list.filter(el => {
        return el.isSelected === true;
      });
    },
    nextCheckItem: function() {
      return this.list
        .filter((el, index) => {
          return el.isSelected;
        })
        .shift();
    }
  },
  components: {
    ControlTable,
    ApplyButton,
    ModalDialog,
    CopyDialog
  },
  created() {
    this.read();
  },
  methods: {
    copyText(text) {},
    totalPrice(index) {
      let entryPrice = 0;
      let totalPrice = 0;
      this.oldList.forEach(index => {
        entryPrice = entryPrice + Number(index.totalPrice);
      });
      totalPrice = Number(entryPrice + this.globalEntry.BBQ * bbqPrice);
      return totalPrice;
    },
    totalDidcountPrice() {
      if (this.oldList.length > 0) {
        let entryPrice = 0;
        let totalPrice = 0;
        this.oldList.forEach(index => {
          entryPrice = entryPrice + Number(index.discountPrice);
        });
        totalPrice = Number(
          entryPrice +
            (this.globalEntry.BBQ * bbqPrice * this.oldList[0].discount) / 100
        );
        return totalPrice;
      }
    },
    read() {
      axios({
        method: "get",
        url: BOOKING_API
      })
        .then(res => {
          this.list = _.cloneDeep(res.data);
          this.globalEntry = _.cloneDeep(res.data.globalEntry);
          this.list = _.cloneDeep(res.data.dailyEntry);
          this.list.forEach(function(element) {
            this.$set(element, "isSelected", false);
          }, this);
          this.oldList = _.cloneDeep(this.list);
          this.showCreateModal = false;
          this.showCopyModal = false;
          console.log(res.data);
        })
        .catch(err => console.log("err", err));
    },
    applyEntries() {
      this.modfiedItemIndexs
        ? this.applyDrinkConfig(this.nextModifiedItem)
        : this.read();
    },
    applyDrinkConfig(element) {
      axios({
        method: "put",
        url: BOOKING_API,
        params: {
          checkin_mon: element.checkin_mon,
          checkin_day: element.checkin_day,
          discount: element.discount,
          addBedNum: element.addBedNum,
          peopleNum: element.peopleNum,
          totalPrice:
            Number(element.price) + Number(element.addBedNum * addBedPrice),
          price: element.price,
          discountPrice:
            element.price * (element.discount / 100) +
            Number(element.addBedNum * addBedPrice)
        }
      })
        .then(res => {
          element.isSelected = false;
          this.read();
        })
        .catch(err => {
          this.read();
        });
    },
    selectEntry(index, checked) {
      if (!checked) {
        this.$set(this.list, index, _.cloneDeep(this.oldList[index]));
      }
      this.$set(this.list[index], "isSelected", checked);
    },
    createList(entry) {
      axios({
        method: "post",
        url: BOOKING_API,
        params: entry
      })
        .then(res => {
          this.read();
        })
        .catch(err => {
          this.read();
        });
    },
    deleteEntry() {
      this.checkItem ? this.toDelete(this.nextCheckItem) : this.read();
    },
    toDelete(element) {
      axios({
        method: "delete",
        url: BOOKING_API,
        params: {
          checkin_mon: element.checkin_mon,
          checkin_day: element.checkin_day
        }
      })
        .then(res => {
          this.read();
        })
        .catch(err => {
          this.read();
        });
    }
  }
};
</script>

<style>
@import "./qq.css";
</style>
