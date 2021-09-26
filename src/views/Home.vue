<template>
  <div class="home">
    <input type="text" v-model="name" placeholder="请输入查询姓名" />示例：张三<br />
    <input type="text" v-model="idcard" placeholder="请输入对比身份证号" />示例：412326199910118174,412326199910118[0-9][1,3,5,7,9]4 <button @click="search">搜索</button><br />
    <span>准确值：{{ confirmIdCard }}</span>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import axios from "axios";
import qs from "qs";
export default defineComponent({
  name: "Home",
  components: {},
  data() {
    return {
      name: "",
      // idcard: "412326199712123324",
      confirmIdCard: "",
      // idcard: "412326199712123[0-9][0,2,4,6,8]4",
      idcard: "412326199712123[0-9]24",
    };
  },
  mounted() {},
  methods: {
    search() {
      console.log("下标", this.idcard.indexOf("["));
      console.log("下标", this.idcard.indexOf("-"));
      console.log("下标", this.idcard.indexOf("]"));
      console.log("下标", this.idcard.lastIndexOf("["));
      console.log("下标", this.idcard.lastIndexOf("]"));
      let cutOut: string = this.idcard.slice(0, this.idcard.indexOf("["));
      console.log(cutOut);
      if (this.idcard.indexOf("[") < this.idcard.indexOf("-") && this.idcard.indexOf("-") < this.idcard.indexOf("]")) {
        console.log(this.idcard.substr(this.idcard.indexOf("[") + 1, 1));
        console.log(this.idcard.substr(this.idcard.indexOf("]") - 1, 1));
        console.log("后面的", this.idcard.substr(this.idcard.lastIndexOf("[") + 1, 1));
        console.log("后面的", this.idcard.substr(this.idcard.lastIndexOf("]") - 1, 1));
        for (var i: number = Number(this.idcard.substr(this.idcard.indexOf("[") + 1, 1)); i <= Number(this.idcard.substr(this.idcard.indexOf("]") - 1, 1)); i++) {
          console.log("i", i);
          if (this.idcard.indexOf("[") == this.idcard.lastIndexOf("[")) {
            let u = this.idcard.substr(this.idcard.indexOf("]") + 1, 1);
            console.log(u);
            let frontSeventeen: string = cutOut + i + u;
            let idcardArr: any = frontSeventeen.split("");
            // console.log(idcardArr);
            var endNumber: any = "";
            if (idcardArr.length == 17) {
              let result: number = (idcardArr[0] * 7 + idcardArr[1] * 9 + idcardArr[2] * 10 + idcardArr[3] * 5 + idcardArr[4] * 8 + idcardArr[5] * 4 + idcardArr[6] * 2 + idcardArr[7] * 1 + idcardArr[8] * 6 + idcardArr[9] * 3 + idcardArr[10] * 7 + idcardArr[11] * 9 + idcardArr[12] * 10 + idcardArr[13] * 5 + idcardArr[14] * 8 + idcardArr[15] * 4 + idcardArr[16] * 2) % 11;
              console.log("第十八位算出的结果为：", (idcardArr[0] * 7 + idcardArr[1] * 9 + idcardArr[2] * 10 + idcardArr[3] * 5 + idcardArr[4] * 8 + idcardArr[5] * 4 + idcardArr[6] * 2 + idcardArr[7] * 1 + idcardArr[8] * 6 + idcardArr[9] * 3 + idcardArr[10] * 7 + idcardArr[11] * 9 + idcardArr[12] * 10 + idcardArr[13] * 5 + idcardArr[14] * 8 + idcardArr[15] * 4 + idcardArr[16] * 2) % 11);
              if (result == 0) {
                endNumber = 1;
              } else if (result == 1) {
                endNumber = 0;
              } else if (result == 2) {
                endNumber = "X";
              } else if (result == 3) {
                endNumber = 9;
              } else if (result == 4) {
                endNumber = 8;
              } else if (result == 5) {
                endNumber = 7;
              } else if (result == 6) {
                endNumber = 6;
              } else if (result == 7) {
                endNumber = 5;
              } else if (result == 8) {
                endNumber = 4;
              } else if (result == 9) {
                endNumber = 3;
              } else if (result == 10) {
                endNumber = 2;
              }
              console.log("第十八位为", endNumber);
              idcardArr.push(endNumber);
              let card = idcardArr.join("");
              frontSeventeen = card;
              console.log("检测最后", frontSeventeen);
              axios
                .post(
                  "http://ai.exocr.com/apqi/api/reco/data/v1/verify_id",
                  qs.stringify({
                    personalName: this.name,
                    identityCardNo: frontSeventeen,
                  })
                )
                .then((res) => {
                  console.log(res)
                  if (!res.data.success) {
                      console.log("需要验证码");
                    } else {
                      if (res.data.result.result_code == 10000) {
                        this.confirmIdCard = frontSeventeen;
                      }
                    }
                });
            }
          } else {
            for (let u: number = Number(this.idcard.substr(this.idcard.lastIndexOf("[") + 1, 1)); u <= Number(this.idcard.substr(this.idcard.lastIndexOf("]") - 1, 1)); u += 2) {
              console.log("u", u);
              console.log("前十七位", cutOut + i + u);
              let frontSeventeen: string = cutOut + i + u;
              let idcardArr: any = frontSeventeen.split("");
              // console.log(idcardArr);
              var endNumber: any = "";
              if (idcardArr.length == 17) {
                let result: number = (idcardArr[0] * 7 + idcardArr[1] * 9 + idcardArr[2] * 10 + idcardArr[3] * 5 + idcardArr[4] * 8 + idcardArr[5] * 4 + idcardArr[6] * 2 + idcardArr[7] * 1 + idcardArr[8] * 6 + idcardArr[9] * 3 + idcardArr[10] * 7 + idcardArr[11] * 9 + idcardArr[12] * 10 + idcardArr[13] * 5 + idcardArr[14] * 8 + idcardArr[15] * 4 + idcardArr[16] * 2) % 11;
                console.log("第十八位算出的结果为：", (idcardArr[0] * 7 + idcardArr[1] * 9 + idcardArr[2] * 10 + idcardArr[3] * 5 + idcardArr[4] * 8 + idcardArr[5] * 4 + idcardArr[6] * 2 + idcardArr[7] * 1 + idcardArr[8] * 6 + idcardArr[9] * 3 + idcardArr[10] * 7 + idcardArr[11] * 9 + idcardArr[12] * 10 + idcardArr[13] * 5 + idcardArr[14] * 8 + idcardArr[15] * 4 + idcardArr[16] * 2) % 11);
                if (result == 0) {
                  endNumber = 1;
                } else if (result == 1) {
                  endNumber = 0;
                } else if (result == 2) {
                  endNumber = "X";
                } else if (result == 3) {
                  endNumber = 9;
                } else if (result == 4) {
                  endNumber = 8;
                } else if (result == 5) {
                  endNumber = 7;
                } else if (result == 6) {
                  endNumber = 6;
                } else if (result == 7) {
                  endNumber = 5;
                } else if (result == 8) {
                  endNumber = 4;
                } else if (result == 9) {
                  endNumber = 3;
                } else if (result == 10) {
                  endNumber = 2;
                }
                console.log("第十八位为", endNumber);
                idcardArr.push(endNumber);
                let card = idcardArr.join("");
                frontSeventeen = card;
                console.log("检测最后", frontSeventeen);
                axios
                  .post(
                    "http://ai.exocr.com/apqi/api/reco/data/v1/verify_id",
                    qs.stringify({
                      personalName: this.name,
                      identityCardNo: frontSeventeen,
                    })
                  )
                  .then((res) => {
                    console.log(res)
                    if (!res.data.success) {
                      console.log("需要验证码");
                    } else {
                      if (res.data.result.result_code == 10000) {
                        this.confirmIdCard = frontSeventeen;
                      }
                    }
                  });
              }
            }
          }
        }
      }

      // let array: any = [];
      // for (let index = 0; index < array.length; index++) {
      //   const element = array[index];
      // }
      // const url: string = ;
      // const api = axios.create({
      //   // url,
      //   timeout: 8000,
      // });
    },
  },
});
</script>
