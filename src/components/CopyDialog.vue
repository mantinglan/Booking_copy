<template>
  <form>
    <transition name="modal">
      <div class="modal-mask">
        <div class="modal-wrapper">
          <div class="modal-container">
            <div class="modal-header">
              <span class="pagetitle text-left col-md-12">Q&Q訂房明細</span>
            </div>
            <div
              class="modal-body"
              id="success_form_input"
              style="white-space: pre;text-align:left; word-break: break-all;"
            >{{parseText()}}</div>
            <div class="modal-footer">
              <button
                type="button"
                class="btn"
                ref="copy"
                data-clipboard-action="copy"
                data-clipboard-target="#success_form_input"
                v-on:click="copyLink"
              >複製文字</button>
              <button v-show="cancelBtn" class="btn" type="button" v-on:click="$emit('close')">取消</button>
              <div
                v-on:focus="(Object.keys(refs).length!=0)?refs.focusFirst.focus():(Object.keys(refs).length!=0)?refs.focusFirst.focus():foucsBtn()"
                tabindex="0"
              ></div>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </form>
</template>

<script>
const bedPrice = 600;
const bbqPrice = 1200;

export default {
  data() {
    return {
      copyBtn: null,
      test: ""
    };
  },
  props: {
    globalEntry: Object,
    daliyEntry: Array,
    height: Number,
    width: {
      type: Number,
      default: 350
    },
    okBtn: {
      type: Boolean,
      default: true
    },
    cancelBtn: {
      type: Boolean,
      default: true
    },
    validator: {
      type: Object,
      default: null
    },
    refs: {
      type: Object,
      default: Object
    },
    lockOk: {
      type: Boolean,
      default: null
    }
  },
  mounted() {
    this.copyBtn = new this.clipboard(this.$refs.copy);
  },
  methods: {
    copyLink() {
      /*這是點選按鈕觸發的點選事件，關於clipboard的使用就不再贅述了，上面介紹時已經講述過，並且使用方法在官方文件上有*/
      let _this = this;
      let clipboard = _this.copyBtn;
    },
    parseText() {
      return (
        this.checkinData() +
        this.daliyData() +
        this.discountData() +
        this.hint()
      );
    },
    checkinData() {
      console.log(this.globalEntry.BBQ);
      let first = `你好👋\n${this.globalEntry.checkin_mon}月${
        this.globalEntry.checkin_day
      }日入住\n${this.globalEntry.checkout_mon}月${
        this.globalEntry.checkout_day
      }日退房\n`;

      let bbq = this.globalEntry.BBQ === 1 ? `有烤肉\n\n` : `無烤肉\n\n`;
      return first + bbq;
    },
    daliyData() {
      let dailyStr = "";
      if (this.daliyEntry.length > 0) {
        let totalPrice = 0;
        let summaryText;
        for (let i = 0; i < this.daliyEntry.length; i++) {
          let totalStr = "";
          let text = `第${i + 1}天${Number(this.daliyEntry[i].peopleNum) +
            Number(this.daliyEntry[i].addBedNum)}人包棟\n`;
          let text2 = `${this.daliyEntry[i].peopleNum}人包棟價$${
            this.daliyEntry[i].price
          }\n加${this.daliyEntry[i].addBedNum}床$${bedPrice *
            this.daliyEntry[i].addBedNum}\n`;
          let text3 = `房間總價$${this.daliyEntry[i].totalPrice}\n\n`;
          totalPrice = totalPrice + Number(this.daliyEntry[i].totalPrice);
          totalStr = text + text2 + text3;
          dailyStr = dailyStr + totalStr;
        }
        summaryText =
          this.globalEntry.BBQ === 1
            ? `總價(含烤肉費)$${totalPrice + bbqPrice}\n`
            : `總價$${totalPrice}\n`;
        dailyStr = dailyStr + summaryText + "-----------------------\n";
      }
      return dailyStr;
    },
    discountData() {
      let disString = "";
      if (this.daliyEntry.length > 0 && this.daliyEntry[0].discount !== 100) {
        let totalDiscountPrice = 0;
        let summaryText;
        for (let i = 0; i < this.daliyEntry.length; i++) {
          let totalStr = "";
          let text = "";
          if (i === 0) {
            text = `💓如果有加入粉絲團現場按讚分享打卡\n且出示確認\n`;
          }

          let text2 = `第${i + 1}天${Number(this.daliyEntry[i].peopleNum) +
            Number(this.daliyEntry[i].addBedNum)}人包棟享${
            this.daliyEntry[i].discount % 10 === 0
              ? this.daliyEntry[i].discount / 10
              : this.daliyEntry[i].discount
          }折\n`;
          let text3 = `打卡房間總價$${this.daliyEntry[i].discountPrice}\n\n`;
          totalStr = text + text2 + text3;
          disString = disString + totalStr;
          totalDiscountPrice =
            totalDiscountPrice + Number(this.daliyEntry[i].discountPrice);
        }
        summaryText =
          this.globalEntry.BBQ === 1
            ? `打卡總價(含烤肉費)$${totalDiscountPrice +
                (bbqPrice * this.daliyEntry[0].discount) / 100}\n`
            : `打卡總價$${totalDiscountPrice}\n`;
        disString = disString + summaryText + "-----------------------\n";
      }
      return disString;
    },
    hint() {
      return `📢我們入住會先收一筆押金$3000元\n於退房時確認無任何違規損壞後全數歸還\n🎤唱歌只能歡唱到到晚上十一點🕚\n🥓烤肉只能烤到晚上十點半🕥`;
    },
    deliever() {
      this.$emit("setting");
      this.$emit("close");
    },
    foucsBtn() {
      this.$refs.okBtn.focus();
    }
  },
  watch: {
    isShowModal() {
      this.parseText();
    }
  }
};
</script>

<style type="text/css">
.stop-scroll {
  overflow: hidden;
}

.dialogBtn {
  width: 112px;
  height: 34px;
  padding-left: 28px;
  padding-right: 28px;
}

.input.is-active,
.input:active,
.input:focus,
.textarea.is-active,
.textarea:active,
.textarea:focus {
  border-color: #00d1b2;
  outline: none;
}

/* dialog */
.modal-mask {
  position: fixed;
  z-index: 2017;
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
  width: 80%;
  height: 80%;
  margin: auto;
  padding: 0px;
  background-color: #F4F4F4;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  max-height: 100%;
  overflow-y: auto;
}

.modal-header {
  /* width: 100%; */
  background-color: #EEE;
  margin-bottom: 16px;
  border-width: thin;
  padding: 10px;
  border: #9C9C9C;
  border-bottom-style: solid;
  color: #000;
}

.modal-body {
  margin: 0px;
  padding: 6px 20px;
}

.modal-footer {
  padding: 10px 20px;
}
</style>
