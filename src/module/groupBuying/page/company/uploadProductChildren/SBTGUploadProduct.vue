<template>
    <ul>
        <li @click="jumpProductCateGory">
            <a>
                <span class="star">设备分类：</span>
                <cube-input placeholder="请选择分类" :disabled="true" v-model="info.productLineName" class="needsclick">
                    <i slot="append"></i>
                </cube-input>
            </a>
        </li>
        <li @click="jumpSelectBrand">
            <a>
                <span class="star">设备品牌：</span>
                <cube-input placeholder="请选择品牌" :disabled="true" v-model="info.brandName" class="needsclick">
                    <i slot="append"></i>
                </cube-input>
            </a>
        </li>
        <li @click="jumpSelectModel">
            <a>
                <span>设备型号：</span>
                <cube-input placeholder="默认为不限" :disabled="true" v-model="info.modelName" class="needsclick">
                    <i slot="append"></i>
                </cube-input>
            </a>
        </li>
        <li class="price">
            <span>团购价格：</span>
            <cube-input placeholder="请输入团购价格" :disabled="false" type="number" v-model.trim.number="info.price" @input="savePriceValue" class="needsclick">
                <span slot="append">万元</span>
            </cube-input>
            <div class="check_box" @click="saveIsOpenValue">
                <cube-checkbox v-model="info.isOpen">
                    对外公开
                </cube-checkbox>
            </div>
        </li>
        <li @click="jumpMainParams">
            <a>
                <span>重要参数：</span>
                <cube-input placeholder="请选择或输入重要参数" :disabled="true" v-model="info.mainParamsName" class="needsclick">
                    <i slot="append"></i>
                </cube-input>
            </a>
        </li>
        <li class="clinic">
            <group>
                <x-textarea title="临床应用:" placeholder="为保证医院的采购质量与效率，请详细填写设备的临床用途" v-model="info.application" @on-change="saveApplicationValue" autosize class="needsclick"></x-textarea>
            </group>
        </li>
    </ul>
</template>
<script>
import { mapMutations } from "vuex";
import { Group, XTextarea } from "vux";
import { Toast } from "vant";
export default {
  data() {
    return { y: 0 };
  },
  computed: {
    info() {
      return this.$store.state.page.uploadProduct.SBTG;
    }
  },
  watch: {
    info() {
      console.log("变化数据：", this.$store.state.page.uploadProduct.SBTG);
    }
  },
  components: {
    Group,
    XTextarea
  },
  methods: {
    ...mapMutations([
      "setTransition",
      "uploadProductSBTGPriceSave",
      "uploadProductSBTGPriceIsOpenSave",
      "uploadProductSBTGApplicationSave"
    ]),
    savePriceValue() {
      this.uploadProductSBTGPriceSave(this.info.price);
    },
    saveApplicationValue() {
      this.uploadProductSBTGApplicationSave(this.info.application);
    },
    saveIsOpenValue() {
      this.uploadProductSBTGPriceIsOpenSave(!this.info.isOpen);
    },
    jumpProductCateGory() {
      if (this.$route.query.groupTypeCode) {
        this.setTransition("turn-on");
        this.$router.push({
          path: "/productCategory",
          query: {
            groupPurchaseTypeId: this.$route.query.groupPurchaseTypeId,
            groupTypeCode: this.$route.query.groupTypeCode,
            page: "uploadProduct",
            vuexSelectValue: this.$store.state.page.uploadProduct.SBTG
              .productLine
          }
        });
      } else {
        Toast("请先选择团购产品类型");
        return;
      }
    },
    jumpSelectBrand() {
      if (this.info.productLineId) {
        this.setTransition("turn-on");
        this.$router.push({
          path: "/selectBrand",
          query: {
            productLineId: this.info.productLineId,
            groupTypeCode: this.$route.query.groupTypeCode,
            page: "uploadProduct",
            vuexSelectValue: this.$store.state.page.uploadProduct.SBTG.brand
          }
        });
      } else {
        Toast("请先选择分类");
        return;
      }
    },
    jumpSelectModel() {
      if (this.info.brandId) {
        this.setTransition("turn-on");
        this.$router.push({
          path: "/selectModel",
          query: {
            brandId: this.info.brandId,
            productLineId: this.info.productLineId,
            groupTypeCode: this.$route.query.groupTypeCode,
            page: "uploadProduct",
            vuexSelectValue: this.$store.state.page.uploadProduct.SBTG.model
          }
        });
      } else {
        Toast("请先选择品牌");
        return;
      }
    },
    jumpMainParams() {
      if (this.$route.query.groupTypeCode) {
        this.setTransition("turn-on");
        this.$router.push({
          path: "/mainParams",
          query: {
            groupTypeCode: this.$route.query.groupTypeCode,
            page: "uploadProduct",
            vuexSelectValue: this.$store.state.page.uploadProduct.SBTG.params
          }
        });
      } else {
        Toast("请先选择团购产品类型");
        return;
      }
    }
  },
  activated() {
    this.info.productLineName = _.join(
      _.map(
        this.$store.state.page.uploadProduct.SBTG.productLine,
        this.$store.state.page.uploadProduct.SBTG.productLine[0].aliasName
          ? "aliasName"
          : "productLineName"
      ),
      ","
    );
    this.info.productLineId = _.join(
      _.map(
        this.$store.state.page.uploadProduct.SBTG.productLine,
        "productLineId"
      ),
      ","
    );
    this.info.brandName = _.join(
      _.map(
        this.$store.state.page.uploadProduct.SBTG.brand,
        this.$store.state.page.uploadProduct.SBTG.brand[0].aliasName
          ? "aliasName"
          : "brandName"
      ),
      ","
    );
    this.info.brandId = _.join(
      _.map(this.$store.state.page.uploadProduct.SBTG.brand, "brandId"),
      ","
    );
    this.info.modelName = _.join(
      _.map(this.$store.state.page.uploadProduct.SBTG.model, "name"),
      ","
    );
    this.info.mainParamsName = _.join(
      _.map(this.$store.state.page.uploadProduct.SBTG.params, "name"),
      ","
    );
    this.$nextTick().then(() => {
      this.$parent.refresh();
      this.$parent.scrollTo(0, this.y);
    });
  },
  beforeRouteLeave(to, from, next) {
    if (to.name != "我的团购（企业）") {
      this.y = this.$parent.scroll.y;
    } else {
      this.y = 0;
    }
    console.log(this.y);
    next();
  }
};
</script>
<style lang="scss" scoped>
ul {
  padding-left: 13px;
  padding-bottom: 1px;
  li {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    height: 47px;
    border-bottom: 0.5px solid #f6f6f6;
    a {
      display: flex;
      justify-content: flex-start;
      align-items: center;
      text-decoration: none;
      width: 100%;
      span {
        font-family: PingFangSC-Regular;
        font-size: 14px;
        color: #333333;
        float: left;
        width: auto;
        &.star {
          padding-left: 7px;
          background: url("../../../../../../static/images/star.png") no-repeat
            left center;
          background-size: 7px;
        }
      }
      > div {
        display: flex;
        justify-content: flex-end;
        padding-right: 13px;
        font-family: PingFangSC-Regular;
        font-size: 14px;
        color: #999999;
        i {
          display: flex;
          height: 14px;
          width: 8px;
          background: url("../../../../../../static/images/grayarrow.png")
            no-repeat center;
          background-size: 100% 100%;
          margin-left: 3px;
        }
        &.maintanceType {
          justify-content: flex-start;
          align-items: center;
          span {
            height: 29px;
            line-height: 29px;
            padding: 0 16px;
            font-family: PingFangSC-Regular;
            font-size: 14px;
            color: #333333;
            margin-right: 10px;
            background: rgba(142, 142, 142, 0.05);
            border-radius: 2px;
            &.active {
              background: rgba(1, 157, 221, 0.14);
              font-family: PingFangSC-Medium;
              color: #019ddd;
            }
          }
        }
      }
    }
    /deep/ .cube-input {
      flex: 1;
      &:after {
        border: none;
      }
      &.responseTime {
        .cube-input-field {
          //text-align: left;
        }
      }
      input {
        color: #999999;
        font-family: PingFangSC-Regular;
        font-size: 14px;
        padding-left: 0;
        text-align: right;
      }
    }
    &.price {
      span {
        font-family: PingFangSC-Regular;
        font-size: 14px;
        color: #333333;
      }
      .check_box {
        height: 100%;
        display: flex;
        align-items: center;
      }
      /deep/ .cube-input {
        padding-right: 16px;
        > .cube-input-append {
          font-family: PingFangSC-Regular;
          font-size: 14px;
          color: #333333;
        }
      }
      /deep/ .cube-checkbox {
        // padding-right: 0;
        display: flex;
        align-items: center;
        padding-left: 0px;
        padding-right: 13px;
        height: 14px;
        border-left: 0.5px solid rgba(153, 153, 153, 0.31);
        &.cube-checkbox_checked {
          .cubeic-right {
            color: #019ddd;
          }
        }
        .cube-checkbox-wrap {
          padding: 0;
          font-family: PingFangSC-Medium;
          font-size: 14px;
          color: #666666;
          display: flex;
          align-items: center;
          height: 0;
          padding-left: 16px;
          border-left: 0.5px solid rgba(153, 153, 153, 0.31);
          .cube-checkbox-ui {
            height: 14px;
            width: 14px;
            margin-right: 4px;
            margin-top: 0px;
          }
          .cube-checkbox-label {
            display: flex;
            height: 100%;
            align-items: center;
          }
          .cube-checkbox-input {
            height: 100%;
          }
        }
      }
    }
    &.clinic {
      height: auto;
      border-bottom: none;
      //margin-bottom: 10px;
      > div {
        width: 100%;
      }
      /deep/ .weui-cells {
        margin-top: 0;
        width: 100%;
        &:before {
          border-top: 0;
        }
        &:after {
          border-bottom: 0;
        }
        .weui-cell {
          display: flex;
          flex-direction: column;
          padding: 0;
          padding-right: 13px;
          .weui-cell__hd {
            height: 43px;
            font-family: PingFangSC-Regular;
            font-size: 14px;
            color: #333333;
            display: flex;
            align-items: center;
            background: url("../../../../../../static/images/star.png")
              no-repeat left center;
            background-size: 7px;
            .weui-label {
              margin-left: 7px;
            }
          }
          .weui-cell__bd {
            width: 100%;
            min-height: 40px;
            textarea::-webkit-input-placeholder {
              font-family: PingFangSC-Regular;
              font-size: 14px;
              color: #cccccc;
            }
            textarea {
              // height: 45px !important;
              color: #999;
              font-family: PingFangSC-Regular;
              font-size: 14px;
              margin-bottom: 12px;
            }
          }
        }
      }
    }
  }
}
</style>
