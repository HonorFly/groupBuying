<template>
    <!-- <router-link :to="{path:'Details',query:{id:dataValue.id}}" @click.native="clickLink" :class="dataValue.status"> -->
    <a @click="clickLink(index)" :class="dataValue.status">
        <div class="listItem">
            <h2>{{dataValue.mainName}}</h2>
            <div class="title">{{dataValue.viceName}}</div>
            <div class="time_box">
                <div class="left">
                    <p>
                        <i></i>
                        <span>{{dataValue.dateArea}}</span>
                    </p>
                    <p>
                        <i></i>
                        <span @click.stop="toNativeMapPage(dataValue)">{{dataValue.addr}}

                        </span>

                    </p>
                </div>
                <div class="right">
                    <span>已参加团购的有:</span>
                    <span>
                        <a>{{dataValue.hospitalNum}}</a>家医院
                        <a>{{dataValue.companyNum}}</a>家企业
                    </span>

                </div>
            </div>
            <i class="topLeft"></i>
            <i class="topRight" :class="addClass"></i>
            <div class="shadow" v-show="isActive">
                <img src="./../../../../../static/images/selectedListItem.png" alt="">
            </div>
        </div>
    </a>
    <!-- </router-link> -->

</template>

<script>
    import index, { mapMutations } from "vuex";
    import selectedListItem from "../../../../../static/images/selectedListItem.png";
    import { ToNativeMap } from "../mixin/mixin";
    export default {
        data() {
            return {};
        },
        mixins: [ToNativeMap],
        methods: {
            ...mapMutations(["setTransition", "setDetailsPageData"]),
            clickLink(index) {
                if (!this.disabled) {
                    this.setTransition("turn-on");
                    this.setDetailsPageData(this.dataValue);
                    this.$router.push({
                        path: `/Details/${this.dataValue.id}`
                    });
                } else {
                    console.log(index);
                    this.$emit("changeIdx", index);
                }
            }
        },
        props: [
            "dataValue",
            "disabled",
            "index",
            "isActive",
            "groupPurchaseTypeId"
        ],
        created() {},
        computed: {
            addClass() {
                switch (this.dataValue.levelName) {
                    case "全国团购":
                        return "National";
                    case "省级团购":
                        return "province";
                    case "月末团购":
                        return "month";
                    default:
                        return;
                }
            }
        }
    };
</script>

<style lang="scss" scoped>
    a {
        text-decoration: none;
        .shadow {
            height: 155px;
            width: 100%;
            position: absolute;
            left: 0;
            top: 0;
            img {
                width: 100%;
                height: 155px;
            }
        }
        .listItem {
            padding: 30px 12px 15.5px;
            position: relative;
            background-color: rgba(255, 255, 255, 0.03);
            border: 0.5px solid rgba(254, 75, 79, 0.2);
            //box-shadow: 0 2px 7px 0 rgba(97,176,42,0.10);
            box-shadow: 0.5px 2px 7px 0.5px rgba(254, 74, 75, 0.09);
            font-family: PingFangSC-Medium;
            color: #333333;
            height: 155px;
            margin-bottom: 10px;
            width: 100%;
            .topLeft,
            .topRight {
                position: absolute;
                display: block;
                height: 20px;
                width: 60px;
                top: 0;
                background: url("../../../../../static/images/progress.png")
                    no-repeat center;
                background-size: 100% 100%;
            }
            .topLeft {
                left: 0;
            }
            .topRight {
                background: url("../../../../../static/images/nationwide.png")
                    no-repeat center;
                right: 0;
                background-size: 100% 100%;
                &.month {
                    background-image: url("../../../../assets/images/month.png");
                }
                &.province {
                    background-image: url("../../../../assets/images/province.png");
                }
            }
            h2 {
                font-size: 12px;
                margin-bottom: 10px;
            }
            .title {
                font-size: 15px;
                margin-bottom: 21.5px;
            }
            .time_box {
                display: flex;
                font-size: 12px;
                .left {
                    width: 50%;
                    color: #999;
                    p {
                        display: flex;
                        justify-content: flex-start;
                        align-items: center;
                        > i {
                            display: flex;
                            height: 13px;
                            width: 14px;
                            background: url("../../../../../static/images/site.png")
                                no-repeat center;
                            background-size: 100% 100%;
                            margin-right: 4px;
                            //margin-top: 2px;
                        }
                        &:first-child {
                            margin-bottom: 17px;
                            i {
                                background: url("../../../../../static/images/timer.png")
                                    no-repeat center;
                                background-size: 100% 100%;
                                height: 14px;
                                width: 14px;
                            }
                        }
                        span {
                            font-family: PingFangSC-Medium;
                            font-size: 12px;
                            color: #333333;
                            display: flex;
                            justify-content: flex-start;
                            align-items: center;
                        }
                    }
                }
                .right {
                    width: 50%;
                    color: #666666;
                    padding-left: 15px;
                    font-family: PingFangSC-Regular;
                    font-size: 10px;
                    color: #666666;
                    padding-top: 2px;
                    span {
                        display: block;
                        &:first-child {
                            margin-bottom: 11.5px;
                        }
                        a {
                            font-family: BebasNeue, Arial, Helvetica, sans-serif;
                            font-size: 24px;
                            color: #feb930;
                            margin-right: 4px;
                            &:last-child {
                                color: #2c94e5;
                                margin-left: 15px;
                            }
                        }
                    }
                }
            }
        }

        &.theEnd {
            .listItem {
                border: 0.5px solid rgba(204, 204, 204, 0.2);
                box-shadow: 0.5px 2px 7px 0.5px rgba(204, 204, 204, 0.19);
            }
            .topLeft {
                background-image: url("../../../../assets/images/end.png");
                background-size: 100% 100%;
            }
        }
        &.begin {
            .listItem {
                border: 0.5px solid rgba(105, 180, 42, 0.2);
                box-shadow: 0.5px 2px 7px 0.5px rgba(97, 176, 42, 0.1);
            }
            .topLeft {
                background-image: url("../../../../assets/images/signup.png");
                background-size: 100% 100%;
            }
        }
    }
</style>
