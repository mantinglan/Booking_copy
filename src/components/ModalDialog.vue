<template v-if="isShowModal">
  <form v-on:submit.prevent="validateBeforeSubmit()">
    <transition name="modal">
      <div class="modal-mask">
        <div class="modal-wrapper">
          <div class="modal-container">
            <div class="modal-header">
              <span>新增住宿日期</span>
            </div>
            <div class="modal-body">
              <div style="text-align:left;margin:15px;">
                <span style="width=50px">入住時間 :</span>
                <select v-model="checkin_mon" style="width:50px; height:20px;">
                  <option v-for="index in 12" v-bind:value="index" v-bind:key="index">{{ index }}</option>
                </select>月
                <select v-model="checkin_day" style="width:50px; height:20px;">
                  <option v-for="index in 31" v-bind:value="index" v-bind:key="index">{{ index }}</option>
                </select>日
              </div>
              <div style="text-align:left;margin:15px;">
                <span style="width=50px">人數 :</span>
                <select v-model="people" style="width:50px; height:20px;">
                  <option v-for="index in 16" v-bind:value="index" v-bind:key="index">{{ index }}</option>
                </select>人
              </div>
              <div style="text-align:left;margin:15px;">
                <span style="width=50px">加床人數 :</span>
                <select v-model="addBedNum" style="width:50px; height:20px;">
                  <option
                    v-for="index in 10"
                    v-bind:value="index-1"
                    v-bind:key="index"
                  >{{ index-1 }}</option>
                </select>人
              </div>
              <div style="text-align:left;margin:15px;">
                <span style="width=50px">總價 :</span>
                <input style="width=160px;" name="price" type="number" v-model.number="price">
              </div>
              <div style="text-align:left;margin:15px;">
                <span>打卡折數 :</span>
                <select v-model="discount" style="width:50px; height:20px;">
                  <option value="100">不打</option>
                  <option
                    v-for="index in 10"
                    v-bind:value="100-((index)*5)"
                    v-bind:key="index"
                  >{{ (index%2 === 0)?(100-((index)*5))/10:(100-((index)*5)) }}</option>
                </select>折
              </div>
            </div>
            <div style="text-align:center">
              <button type="submit" style="margin:15px;">Submit</button>
              <button type="button" style="margin:15px;" v-on:click="cancel()">Cancel</button>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </form>
</template>

<script>
const addBedPrice = 600;
function getDefaultData() {
  return {
    checkin_mon: 1,
    checkin_day: 1,
    people: 10,
    addBedNum: 0,
    discount: 100,
    totalPrice: 0,
    price: 0,
    discountPrice: 0
  };
}

export default {
  data() {
    return getDefaultData();
  },
  props: {
    isShowModal: Boolean
  },
  methods: {
    cancel() {
      this.$emit("notifyShowModal");
    },
    validateBeforeSubmit() {
      let entry = {};
      entry.checkin_mon = this.checkin_mon;
      entry.checkin_day = this.checkin_day;
      entry.discount = this.discount;
      entry.addBedNum = this.addBedNum;
      entry.peopleNum = this.people;
      entry.totalPrice =
        Number(this.price) + Number(this.addBedNum * addBedPrice);
      entry.price = this.price;
      entry.discountPrice =
        (this.price + Number(this.addBedNum * addBedPrice)) *
        (this.discount / 100);
      // entry.discountPrice =
      //   this.price+
      //   Number(this.addBedNum * addBedPrice) * (this.discount / 100) ;
      this.$emit("createEntry", entry);
    }
  },
  watch: {
    isShowModal() {
      _.assign(this.$data, getDefaultData());
    }
  }
};
</script>

<style type="text/css">
.stop-scroll {
  overflow: hidden;
}

/* dialog */
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 320px;
  /* height: 380px; */
  margin: 0px auto;
  padding: 0px;
  background-color: #F4F4F4;
  border-radius: 5px;
  transition: all 0.3s ease;
  max-height: 100%;
  max-width: 100%;
  overflow-y: auto;
}

.modal-header {
  background-color: #EEE;
  margin-bottom: 16px;
  padding: 10px;
  border: #9C9C9C;
  border-bottom-style: solid;
  color: #000;
  text-align: center;
  font-size: 20px;
}

.modal-body {
  margin: 0px;
  padding: 6px 20px;
}

.modal-footer {
  padding: 10px 20px;
}
</style>
