<style lang="stylus" scoped>
close-btn-size = 20px

colorBg = #303030
colorText1 = #e7e7e7
colorText2 = mix(colorText1, black, 90%)
colorText3 = mix(colorText1, black, 80%)
colorText4 = mix(colorText1, black, 65%)
colorText5 = mix(colorText1, black, 50%)


button-submit()
  border-color = #d7d7d7
  text-color = #dcdcdc
  bg-color = #333333

  border-color-hover = #ffffff
  text-color-hover = #ffffff

  color text-color
  border border-color solid 0px
  border-radius 999999999px
  text-shadow none
  transition all 0.2s ease
  cursor pointer
  box-sizing border-box
  margin auto
  width 70%
  padding 10px 20px
  min-height 40px
  background bg-color
  display flex
  align-items center
  justify-content center

  &:hover
    color text-color-hover
    border-color border-color-hover

.modal
  position fixed
  top 0
  left 0
  width 100%
  height 100vh
  z-index 999
  backdrop-filter blur(10px)
  opacity 1
  transition opacity 0.2s ease

  .modal-background
    position fixed
    left 0
    top 0
    width 100%
    height 100vh
    background-color black
    opacity 0.6
  .form
    position relative
    margin 150px auto
    max-width 600px
    background colorBg
    padding 20px
    border-radius 10px
    @media (max-width: 600px)
      margin-left 10px
      margin-right 10px

    .confirm-button
      width 45%
      display inline-block
      text-align center
      margin-left 2.5%
      margin-right 2.5%

    .close-btn
      position absolute
      color colorText3
      right 20px
      top 10px
      width close-btn-size
      height close-btn-size
      transition all 0.2s ease
      cursor pointer
    .close-btn:hover
      color colorText1
      text-shadow colorText1
      transform scale(1.1)

    .info-container
      .title
        line-height 1.2
        font-size 1.2rem
        color colorText1
        margin-bottom 10px
      .description
        line-height 1.2
        font-size 1.0rem
        color colorText3

    .fields-container
      .form-group
        .input
          all unset
          box-sizing border-box
          width 100%
          padding 5px 15px
          line-height 1.2
          font-size 1.2rem
          color colorText1
          border 2px solid colorText1
          border-radius 99999999px
          margin-top 10px
    .submit-container
      .confirm-buttons
        display flex
        gap 20px
      .confirm-button
        line-height 1.2
        font-size 1.2rem
        button-submit()
        margin-top 20px
        width 50%

.modal.hidden
  opacity 0
  pointer-events none
</style>

<template>
  <div class="modal" :class="{hidden: !isShowed}" @keydown.enter.prevent="__resolve(true)" @keydown.esc="__resolve(false)">
    <div class="modal-background" @click="__resolve(false)"></div>

    <div class="form" ref="form">
      <span class="close-btn" @click="__resolve(false)">
        <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 16 16"><path d="M1.293 1.293a1 1 0 0 1 1.414 0L8 6.586l5.293-5.293a1 1 0 1 1 1.414 1.414L9.414 8l5.293 5.293a1 1 0 0 1-1.414 1.414L8 9.414l-5.293 5.293a1 1 0 0 1-1.414-1.414L6.586 8 1.293 2.707a1 1 0 0 1 0-1.414z"/></svg>
      </span>

      <div class="info-container">
        <div class="title text-big-x">{{ title }}</div>
        <div class="description text-small">{{ description }}</div>
      </div>

      <div class="fields-container">
        <div v-if="type === Types.prompt" class="form-group">
          <input type="text" v-model="text" ref="inputText" class="input" :placeholder="placeholder">
        </div>
      </div>

      <div class="submit-container submit-buttons">
        <div class="form-group">
          <button @click="__resolve(true)" v-if="type !== Types.confirm" class="confirm-button" ref="buttonOk">Ок</button>
          <div v-else class="confirm-buttons">
            <button @click="__resolve(true)" class="confirm-button" ref="buttonYes">Да</button>
            <button @click="__resolve(false)" class="confirm-button btn-danger">Нет</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        title: "",
        description: "",
        isShowed: false,
        text: "",
        placeholder: "",

        Types: {
          prompt: 0,
          confirm: 1,
          alert: 2,
        },
        type: 0,
      };
    },

    unmounted() {
      this.isShowed = false;
    },

    methods: {
      async __createModal(title, description = '', placeholder='', type='alert') {
        this.type = type;
        this.title = title;
        this.description = description;
        this.placeholder = placeholder;

        if (this.isShowed) {
          return this.promise;
        }
        this.isShowed = true;

        await this.$nextTick();

        if (this.type === this.Types.confirm) {
          this.$refs.buttonYes.focus();
        } else if (this.type === this.Types.prompt) {
          this.$refs.inputText.focus();
        } else {
          this.$refs.buttonOk.focus();
        }

        const promise = new Promise((resolve) => {
          this.resolvePromise = resolve;
        });
        this.promise = promise;

        return promise;
      },
      __resolve(result) {
        if (!this.isShowed) {
          return;
        }

        if (this.type !== this.Types.confirm) {
          if (result === false) {
            result = null;
          } else {
            result = this.text;
          }
        }

        this.resolvePromise(result);
        this.isShowed = false;
        this.text = '';
      },

      prompt(title, description, defaultText, placeholder) {
        this.text = defaultText;
        return this.__createModal(title, description, placeholder, this.Types.prompt);
      },
      confirm(title, description) {
        return this.__createModal(title, description, '', this.Types.confirm);
      },
      alert(title, description) {
        return this.__createModal(title, description, '', this.Types.alert);
      },
    }
  }
</script>
