<template>
  <div id="form-section" class="form-wrapper">
    <img
      v-if="isMobileBg"
      class="wave-bg"
      src="../assets/img/images/background.png"
      alt="wave"
    />
    <img
      v-else
      class="wave-bg"
      src="../assets/img/images/background-mobile2.png"
      alt="wave"
    />
    <img class="snow-bg" src="../assets/img/images/snow-bg.png" alt="snow" />
    <div class="form-inner container" id="form-anchor">
      <h2 class="form-title">
        Замовте персональне відеопривітання від Діда Мороза
      </h2>
      <p class="form-subtitle">
        <span>{{ peoples }} людей</span> замовляють відео прямо зараз...
      </p>
      <validation-observer ref="observer">
        <form @submit.prevent="formSubmit">
          <div class="drop-zone" @click="loadImage">
            <div class="drop-zone-area">
              <validation-provider
                ref="portraitImg"
                name="Portrait"
                rules="required|size:10000|image|ext:jpg,png"
                v-slot="{ errors, validate }"
              >
                <input
                  type="file"
                  ref="fileInput"
                  class="fileInput"
                  accept="image/*"
                  @change="loadFile"
                />
                <span style="color: red">{{ errors[0] }}</span>
              </validation-provider>
              <div class="drop-zone-placeholder" v-if="!preview">
                <img
                  src="../assets/img/icons/cloud-computing 1.svg"
                  alt="cloud"
                />
                <p class="drop-zone-title font-alt">Завантажте фото дитини</p>
                <p class="drop-zone-subtitle font-alt">
                  або перетягніть файл в цю область
                </p>
              </div>
              <div class="drop-zone-placeholder" v-else>
                <p>
                  <img :src="preview" id="output" width="200" height="250" />
                </p>
                <p class="drop-zone-subtitle extra-margin font-alt">
                  Обрано файл {{ image }}
                </p>
                <p class="drop-zone-subtitle font-alt" @click="loadImage">
                  Обрати інший
                </p>
              </div>
            </div>
            <div class="drop-zone-details">
              <div class="item">
                <img src="../assets/img/icons/check.svg" alt="check" />
                <p class="font-alt">формат: <span>PNG, JPG</span></p>
              </div>
              <div class="item">
                <img src="../assets/img/icons/check.svg" alt="check" />
                <p class="font-alt">ширина: <span>від 600px</span></p>
              </div>
              <div class="item">
                <img src="../assets/img/icons/check.svg" alt="check" />
                <p class="font-alt">розмір: <span>2 - 10 МБ</span></p>
              </div>
              <div class="item">
                <img src="../assets/img/icons/check.svg" alt="check" />
                <p class="font-alt">орієнтація: <span></span></p>
                <img
                  src="../assets/img/icons/vertical 1.svg"
                  alt="pic"
                  class="icon-vertical"
                />
              </div>
            </div>
          </div>
          <div class="fields-group">
            <div class="form-row">
              <div class="user-lang">
                <div class="field dropdown">
                  <p class="field-label font-alt">Оберіть мову</p>
                  <b-form-select
                    v-model.trim="langSelected"
                    :options="langOptions"
                    name="language"
                    id="language"
                  ></b-form-select>
                </div>
              </div>
              <div class="user-sex">
                <div class="field radio">
                  <p class="field-label font-alt">Стать</p>
                  <p>
                    <input
                      type="radio"
                      id="boy"
                      name="radio-group"
                      v-model="sexSelected"
                      value="boy"
                      checked
                    />
                    <label for="boy" class="font-alt">Хлопчик</label>
                  </p>
                  <p>
                    <input
                      type="radio"
                      id="girl"
                      name="radio-group"
                      v-model="sexSelected"
                      value="girl"
                    />
                    <label for="girl" class="font-alt">Дівчинка</label>
                  </p>
                </div>
              </div>
              <div class="user-name">
                <div class="field dropdown">
                  <p class="field-label font-alt">Ім’я дитини</p>
                  <validation-provider
                    name="Name"
                    rules="required"
                    v-slot="validationContext"
                  >
                    <b-form-select
                      v-model="nameSelected"
                      :options="sortedNames"
                      :state="getValidationState(validationContext)"
                    ></b-form-select>
                  </validation-provider>
                </div>
              </div>
              <div class="user-email">
                <div class="field">
                  <p class="field-label font-alt">Ваш e-mail</p>
                  <validation-provider
                    name="Email"
                    rules="required|email"
                    v-slot="validationContext"
                  >
                    <b-form-input
                      v-model="userEmail"
                      placeholder="На цю пошту ми відправимо вам відео"
                      :state="getValidationState(validationContext)"
                    ></b-form-input>
                  </validation-provider>
                </div>
              </div>
            </div>
            <div class="form-row">
              <p class="name-support font-alt">
                Не знайшли потрібне ім’я?<span @click="sendNewName">
                  Напишіть нам!</span
                >
              </p>
            </div>
            <div class="form-row">
              <div class="user-agreement">
                <div class="field checkbox">
                  <p>
                    <input
                      type="checkbox"
                      id="offer"
                      name="radio-offer"
                      v-model="isOfferAgree"
                    />
                    <label for="offer" class="font-alt"
                      >Я приймаю <span>умови публічної оферти</span> та
                      <span>політики обробки персональних данних.</span></label
                    >
                  </p>
                </div>
              </div>
              <div class="user-capcha">
                <p class="font-alt">Підтвердіть, що ви не робот:</p>
                <div class="capcha">
                  <vue-recaptcha
                    sitekey="6LfcCKsaAAAAAB9QsW7CsVMjmpA6ITulhy6XBgyI"
                    :loadRecaptchaScript="true"
                    @verify="markRecaptchaAsVerified"
                  ></vue-recaptcha>
                </div>
              </div>
            </div>
          </div>
          <button type="submit" class="bg-grad font-alt submit-btn">
            замовити відеопривітання
          </button>
        </form>
      </validation-observer>
    </div>
    <name-popup v-if="isNamePopupActive"></name-popup>
    <div class="result"></div>
  </div>
</template>

<script>
import VueRecaptcha from "vue-recaptcha";
import NamePopup from "../components/NamePopup.vue";
import { $bus } from "../main";

export default {
  components: {
    VueRecaptcha,
    NamePopup,
  },

  data() {
    return {
      isNamePopupActive: false,
      preview: null,
      imageFile: null,
      isOfferAgree: false,
      userEmail: "",
      sexSelected: "boy",
      nameSelected: "",
      langSelected: "UA",
      namesJson: null,
      langOptions: [
        { value: "UA", text: "Українська" },
        { value: "RU", text: "Російська" },
      ],
      recaptchaVerified: false,
    };
  },

  computed: {
    isBoy() {
      if (this.sexSelected === "boy") {
        return true;
      } else if (this.sexSelected === "girl") {
        return false;
      }
      return null;
    },

    orderDay() {
      return new Date().getTime().toString();
    },

    nameIndicator() {
      if (this.sexSelected === "boy" && this.langSelected === "UA") {
        return "man_ua";
      } else if (this.sexSelected === "boy" && this.langSelected === "RU") {
        return "man_rus";
      } else if (this.sexSelected === "girl" && this.langSelected === "UA") {
        return "woman_ua";
      } else if (this.sexSelected === "girl" && this.langSelected === "RU") {
        return "woman_rus";
      }
      return false;
    },

    sortedNames() {
      if (this.namesJson && this.nameIndicator) {
        const arr = this.namesJson[0]?.contents.find(
          (i) => i.name === this.nameIndicator && i.type === "directory"
        );
        const placeholder = { value: "", text: "Оберіть ім’я зі списку" };
        const sortedArray = arr?.contents.map((i) => {
          return {
            value: i.name,
            text: i.name.split(".")[0],
          };
        });
        sortedArray.unshift(placeholder);
        return sortedArray;
      }
      return null;
    },

    peoples() {
      return Math.floor(Math.random() * 15);
    },

    isMobileBg() {
      return window.screen.width < 576;
    },

    isMobile() {
      return window.screen.width < 991;
    },
  },

  mounted() {
    this.getNamesJson();
    $bus.$on("close-name-popup", () => {
      this.isNamePopupActive = false;
    });

    const recaptchaScript = document.createElement("script");
    this.setAttributes(recaptchaScript, {
      id: "widget-wfp-script",
      language: "javascript",
      type: "text/javascript",
      src: "https://secure.wayforpay.com/server/pay-widget.js?ref=button",
    });
    document.head.appendChild(recaptchaScript);
  },

  methods: {
    runWfpWdgt(url) {
      const wayforpay = new Wayforpay();
      wayforpay.invoice(url);
    },

    setAttributes(el, attrs) {
      for (let key in attrs) {
        el.setAttribute(key, attrs[key]);
      }
    },

    sendNewName() {
      (this.isNamePopupActive = true),
        (document.querySelector("body").style.overflow = "hidden");
    },

    loadImage() {
      const fileInputFiled = this.$refs.fileInput;
      fileInputFiled.click();
    },

    formSubmit() {
      this.$refs.observer.validate().then((success) => {
        if (!success || !this.isOfferAgree) {
          alert("Заповніть всі поля");
        } else {
          this.checkIfRecaptchaVerified();
          this.runScript();
        }
      });
    },

    async loadFile(event) {
      const input = event.target;
      const { valid } = await this.$refs.portraitImg.validate(
        Array.from(input.files)
      );
      if (!valid) {
        alert("Зображення не відповідає вимогам");
        return;
      }
      if (input.files && input.files[0]) {
        const reader = new FileReader();
        reader.onload = (e) => {
          this.preview = e.target.result;
        };
        this.imageFile = input.files[0];
        reader.readAsDataURL(input.files[0]);
        if (!this.isMobile) {
          document.querySelector(".drop-zone").style.height = "520px";
        }
      }
    },

    getNamesJson() {
      fetch(
        "https://test.did-moroz.video/assets/video_assets/AAC/filelist.json",
        { headers: { "Access-Control-Allow-Origin": "*" } }
      )
        .then((resp) => {
          if (resp.ok) {
            return resp.json();
          } else {
            console.error("Request failed");
          }
        })
        .then((data) => {
          this.namesJson = data;
        })
        .catch((error) => {
          console.error("Request failed", error);
        });
    },

    markRecaptchaAsVerified(response) {
      this.recaptchaVerified = true;
    },

    checkIfRecaptchaVerified() {
      if (!this.recaptchaVerified) {
        alert("Будь ласка натисніть на recaptcha");
        return;
      }
      this.runWfpWdgt("https://secure.wayforpay.com/button/b4532b645dfee");
    },

    getValidationState({ dirty, validated, valid = null }) {
      return dirty || validated ? valid : null;
    },

    runScript() {
      console.log("run script");
    },
  },
};
</script>

<style lang="scss">
#form-section {
  height: 130vh;
  width: 100%;
  z-index: 4;
  position: relative;
  padding-top: 165px;
  @include respond-to("all-mobile") {
    height: 150vh;
    padding: 20vh 10px 0;
  }
  .wave-bg {
    width: 100%;
    height: 160vh;
    position: absolute;
    top: 0;
    left: 0;
    @include respond-to("all-mobile") {
      object-fit: cover;
    }
  }
  .snow-bg {
    position: absolute;
    top: 10vh;
    left: 0;
    width: 100%;
    height: 100%;
    object-fit: none;
    @include respond-to("all-mobile") {
      object-fit: cover;
    }
  }
  .form {
    @extend .font-alt;
    &-inner {
      position: relative;
      padding: 97px;
      background: url("../assets/img/images/Component 2.svg") no-repeat;
      background-size: cover;
      border-radius: 25px;
      z-index: 10;
      @include respond-to("all-mobile") {
        padding: 75px 15px 30px;
      }
      &::before {
        content: "";
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        width: 100%;
        height: 100px;
        background: url("../assets/img/images/lights.png") no-repeat;
        background-size: cover;
        @include respond-to("all-mobile") {
          background-image: url("../assets/img/images/light-mobile.svg");
          height: 80px;
        }
      }

      form {
        .drop-zone {
          background: $drop-zone-bg;
          border: 1px dashed #9a9a9a;
          box-sizing: border-box;
          border-radius: 8px;
          height: 220px;
          margin-bottom: 57px;
          position: relative;
          @include respond-to("all-mobile") {
            height: auto;
            margin-bottom: 35px;
            padding-top: 10px;
          }
          &-area {
            height: 100%;
            cursor: pointer;
            .fileInput {
              opacity: 0;
              height: 100%;
              height: 100%;
              left: 0;
              position: absolute;
              top: 0;
              bottom: 0;
              input {
                height: 100%;
              }
            }
          }
          &-placeholder {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            position: absolute;
            width: 100%;
            top: 20px;
            @include respond-to("all-mobile") {
              position: relative;
              top: 0;
            }
            img {
              height: 80px;
              width: 80px;
              object-fit: cover;
              @include respond-to("all-mobile") {
                width: 40px;
                height: 40px;
              }
            }
            p {
              img {
                height: 400px;
                width: 250px;
                @include respond-to("all-mobile") {
                  width: 125px;
                  height: 200px;
                }
              }
            }
          }
          &-title {
            color: $black;
            font-weight: 700;
            margin: 7px 0 4px;
            @include respond-to("all-mobile") {
              font-size: 14px;
              line-height: 20px;
              margin: 0 0 10px;
            }
          }
          &-subtitle {
            color: $grey;
            @include respond-to("all-mobile") {
              display: none;
            }
            &.extra-margin {
              margin: 7px 0 4px;
            }
          }
          &-details {
            display: flex;
            flex-direction: row;
            justify-content: center;
            align-items: center;
            position: absolute;
            bottom: 18px;
            width: 100%;
            @include respond-to("all-mobile") {
              bottom: 4px;
              flex-wrap: wrap;
              position: relative;
            }
            .item {
              display: flex;
              flex-direction: row;
              justify-content: center;
              align-items: center;
              margin: 0 15px;
              @include respond-to("all-mobile") {
                margin: 0 0 4px;
                justify-content: start;
                width: 45%;
              }
              img {
                @include respond-to("all-mobile") {
                  height: 14px;
                  width: 14px;
                }
              }
              &:last-child {
                p {
                  margin-right: 4px;
                }
              }
              p {
                color: $black;
                margin-left: 8px;
                @include respond-to("all-mobile") {
                  font-size: 12px;
                  margin-left: 4px;
                }
                span {
                  color: $black;
                  font-weight: 500;
                }
              }
              .icon-vertical {
                height: 18px;
                width: 17px;
              }
            }
          }
        }
        .fields-group {
          padding: 0 5px;
        }
        button {
          display: flex;
          font-weight: 600;
          margin: 27px auto 0;
          @include respond-to("all-mobile") {
            margin-top: 21px;
          }
        }
      }
    }
    &-title {
      font-size: 30px;
      line-height: 43px;
      font-weight: bold;
      text-align: center;
      @include respond-to("all-mobile") {
        font-size: 18px;
        line-height: 26px;
        display: inline-block;
        width: 70%;
      }
    }
    &-subtitle {
      font-size: 16px;
      line-height: 23px;
      font-weight: 600;
      text-align: center;
      margin: 25px 0 32px;
      @include respond-to("all-mobile") {
        font-size: 14px;
        line-height: 20px;
        margin: 10px 0 20px;
        display: inline-block;
        width: 70%;
      }
      span {
        color: $yellow;
      }
    }
    &-row {
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      position: relative;
      &:first-child {
        @include respond-to("all-mobile") {
          flex-wrap: wrap;
        }
      }
      &:last-child {
        @include respond-to("all-mobile") {
          display: flex;
          flex-direction: column;
        }
      }
      .field.checkbox {
        display: flex;
        align-items: center;
      }
      .user {
        &-lang {
          width: 17%;
          @include respond-to("all-mobile") {
            width: 50%;
          }
        }
        &-sex {
          width: 13%;
          @include respond-to("all-mobile") {
            width: 40%;
          }
          .radio {
            display: flex;
            flex-direction: column;
            justify-content: space-around;
            align-items: flex-start;
          }
        }
        &-name {
          width: 27%;
          @include respond-to("all-mobile") {
            width: 100%;
            margin: 42px 0 91px;
          }
        }
        &-email {
          width: 39%;
          @include respond-to("all-mobile") {
            width: 100%;
          }
        }
        &-agreement,
        &-capcha {
          width: 48%;
          @include respond-to("all-mobile") {
            width: 100%;
          }
        }
        &-agreement {
          display: flex;
          align-items: center;
          @include respond-to("all-mobile") {
            margin: 16px 0 12px;
          }
          label {
            text-align: left;
            span {
              font-weight: 700;
              cursor: pointer;
            }
          }
        }

        &-capcha {
          display: flex;
          justify-content: center;
          align-items: center;
          p {
            text-align: left;
            @include respond-to("all-mobile") {
              line-height: 20px;
            }
          }
          > div > div {
            > div {
              @include respond-to("all-mobile") {
                width: 100% !important;
              }
              iframe {
                @include respond-to("all-mobile") {
                  width: 100% !important;
                }
              }
            }
          }
        }
      }
      .name-support {
        padding-left: 34px;
        position: relative;
        line-height: 23px;
        font-weight: 500;
        margin: 10px 0 17px;
        @include respond-to("all-mobile") {
          position: absolute;
          top: -160px;
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: start;
          line-height: 20px;
        }
        span {
          font-weight: 700;
          cursor: pointer;
        }
        &::before {
          content: "";
          display: inline-block;
          position: absolute;
          left: 0;
          background: url("../assets/img/icons/question.svg");
          width: 22px;
          height: 22px;
        }
      }
    }
  }
}
.field.dropdown {
  position: relative;
  &::before {
    content: "";
    position: absolute;
    right: 17px;
    top: 30px;
    height: 17px;
    width: 17px;
    background: url("../assets/img/icons/arrow down.svg") no-repeat;
    z-index: 1;
    @include respond-to("all-mobile") {
      width: 22px;
      height: 22px;
    }
  }
  &.active {
    &::before {
      transform: rotateZ(180deg);
      top: 22px;
    }
  }
}

.submit-btn {
  &.disabled {
    cursor: unset;
    background-color: grey;
    color: $black;
  }
}
</style>
