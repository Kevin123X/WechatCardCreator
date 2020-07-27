<template>
  <div class="creator">
    <div class="left">
      <el-form ref="form" :model="form" label-width="40px">
        <el-form-item
          :label="item.label"
          v-for="(item, index) in forms"
          :key="index"
        >
          <el-input
            v-model="form[item.key]"
            v-if="item.type == 'input'"
            :placeholder="`请输入你的${item.label}`"
            clearable
          ></el-input>
          <el-date-picker
            v-model="form[item.key]"
            type="date"
            placeholder="选择日期时间"
            v-if="item.type == 'date-picker'"
            value-format="yyyy-MM-dd"
            style="width:100%"
          >
          </el-date-picker>
          <div class="avatar-uploader" v-if="item.type === 'upload'">
            <img :src="form.PHOTO" class="avatar" />
            <i
              class="el-icon-refresh"
              :class="{ iconRotate: isRotate }"
              @click="handleRefreshAvatar"
            ></i>
          </div>
        </el-form-item>
        <el-button type="primary" @click="handleCreateCrad">生成名片</el-button>
      </el-form>
    </div>
    <i class="el-icon-right"></i>
    <div class="right">
      <div class="card" id="card">
        <div class="cl" v-if="info.N">
          <div>
            <img :src="info.PHOTO" alt="" v-if="info.PHOTO" class="photo" />
            <span>{{ info.N }}</span
            ><span>{{ info.NICKNAME ? `（${info.NICKNAME}）` : "" }}</span>
          </div>
          <span v-if="info.BDAY"
            ><i class="iconfont icon-shengri"></i>{{ info.BDAY }}</span
          >
          <span v-if="info.TEL"
            ><i class="iconfont icon-telephone"></i>{{ info.TEL }}</span
          >
          <span v-if="info.EMAIL"
            ><i class="iconfont icon-youxiang"></i>{{ info.EMAIL }}</span
          >
          <span v-if="info.ADR"
            ><i class="iconfont icon-dizhi"></i>{{ info.ADR }}</span
          >
        </div>
        <div class="cr" v-if="hasCode">
          <div id="qrcode"></div>
          <span>微信扫一扫</span>
        </div>
      </div>
      <br />
      <br />
      <el-button type="danger" style="margin-left" @click="handleSaveCard"
        >保存名片</el-button
      >
    </div>
  </div>
</template>

<script>
import html2canvas from "html2canvas";
import QRCode from "qrcodejs2";
import avatars from "./avatar";
export default {
  name: "Creator",
  data() {
    return {
      form: {
        N: "",
        NICKNAME: "",
        PHOTO: "",
        BDAY: "",
        TEL: "",
        EMAIL: "",
        ADR: ""
      },
      forms: [
        {
          label: "姓名",
          key: "N",
          type: "input"
        },
        {
          label: "昵称",
          key: "NICKNAME",
          type: "input"
        },
        {
          label: "头像",
          key: "PHOTO",
          type: "upload"
        },
        {
          label: "生日",
          key: "BDAY",
          type: "date-picker"
        },
        {
          label: "手机",
          key: "TEL",
          type: "input"
        },
        {
          label: "邮箱",
          key: "EMAIL",
          type: "input"
        },
        {
          label: "地址",
          key: "ADR",
          type: "input"
        }
      ],
      active: Math.ceil(Math.random() * 7),
      avatars,
      info: {},
      isRotate: false,
      hasCode: false
    };
  },
  mounted() {
    this.form.PHOTO = this.avatars[this.active];
  },
  methods: {
    handleRefreshAvatar() {
      this.isRotate = !this.isRotate;
      const iIndex = Math.ceil(Math.random() * 7);
      this.form.PHOTO = this.avatars[iIndex];
    },
    handleCheckParams() {
      if (!this.form.N) {
        this.$message.error("请输入你的姓名");
        return false;
      }
      return true;
    },
    handleCreateCrad() {
      if (this.handleCheckParams()) {
        this.info = JSON.parse(JSON.stringify(this.form));
        this.handleCreateQrCode();
      }
    },
    handleCreateQrCode() {
      this.hasCode = true;
      const form = this.form;
      let vcard = "BEGIN:VCARD";
      for (const key in form) {
        if (Object.prototype.hasOwnProperty.call(form, key)) {
          const e = form[key];
          if (key !== "PHOTO") {
            vcard = vcard + "\n" + key + ":" + e;
          }
        }
      }
      vcard = vcard + "\nEND:VCARD";
      this.$nextTick().then(() => {
        new QRCode("qrcode",{
          text:vcard,
          correctLevel : QRCode.CorrectLevel.H
        });
      });
    },
    handleSaveCard() {
      html2canvas(document.getElementById("card")).then(function(canvas) {
        let img = canvas.toDataURL();
        let aTag = document.createElement("A");
        aTag.download = "card";
        aTag.href = img;
        aTag.click();
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang='less'>
.avatar-uploader {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  .avatar {
    display: block;
    width: 65px;
    height: 65px;
    border-radius: 4px;
    overflow: hidden;
  }
  .el-icon-refresh {
    font-size: 24px;
    margin-left: 10px;
    cursor: pointer;
    color: teal;
    transition: all 0.3s linear;
  }
  .iconRotate {
    transform: rotateZ(180deg);
  }
}

.creator {
  display: flex;
  flex-wrap: nowrap;
  align-items: center;
  justify-content: center;
  .left {
    width: 300px;
    padding: 30px 20px;
    border: 1px solid #eeeeee;
    border-radius: 6px;
    box-shadow: 0 1px 10px #dddddd;
    .el-form-item {
      text-align: left;
    }
  }
  .el-icon-right {
    font-size: 64px;
    color: teal;
    margin: 0 40px;
  }
  .right {
    .card {
      width: 480px;
      height: 289px;
      background: teal;
      box-sizing: border-box;
      padding: 20px;
      color: #ffffff;
      display: flex;
      flex-wrap: nowrap;
      align-items: center;
      justify-content: space-between;
      .cl {
        flex: 1;
        width: 0;
        height: 100%;
        span {
          color: #ffffff;
          font-size: 16px;
        }
        .iconfont {
          margin-right: 15px;
        }
        & > div {
          display: flex;
          flex-wrap: nowrap;
          align-items: flex-end;
          justify-content: flex-start;
          margin-bottom: 30px;
          img {
            display: block;
            width: 65px;
            height: 65px;
            border-radius: 4px;
            margin-right: 20px;
          }
          span:first-of-type {
            font-size: 28px;
            vertical-align: baseline;
            margin-bottom: 0;
          }
          span:last-of-type {
            vertical-align: baseline;
            margin-bottom: 0;
          }
        }
        span {
          display: block;
          margin-bottom: 20px;
          text-align: left;
        }
      }
      .cr {
        display: flex;
        flex-direction: column;
        justify-content: flex-end;
        align-items: flex-end;
        height: 100%;
        /deep/#qrcode {
          width: 80px;
          height: 80px;
          overflow: hidden;
          border: 5px solid #ffffff;
          margin-bottom: 6px;
          img {
            width: 80px !important;
            display: block;
          }
        }
        span {
          display: block;
          width: 100%;
          text-align: center;
          font-size: 14px;
        }
      }
    }
  }
}
</style>
