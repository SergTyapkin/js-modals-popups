<style lang="stylus">
  indent = 15px
  innerPadding = 10px
  titleMargin = 5px

  messageWidth = 225px

  clBgPrimarySuccess = #28A745
  clBgSecondarySuccess = #218838
  clBgPrimaryAlert = #d9d23f
  clBgSecondaryAlert = #b89f24
  clBgPrimaryError = #E54D42
  clBgSecondaryError = #B82E24

  borderRadius = 2px

  .popup-messages
    pointer-events none
    position fixed
    top indent
    right indent
    width messageWidth
    z-index 1000
    font-family Arial
    .popup-message
      box-sizing content-box
      overflow hidden
      margin-bottom indent
      padding innerPadding
      border-radius borderRadius
      color textColor1
      opacity 1
      .title
        padding-bottom titleMargin
    .popup-message
      background-color clBgPrimarySuccess
      border-left 5px solid clBgSecondarySuccess
    .popup-message.error
      background-color clBgPrimaryError
      border-left-color clBgSecondaryError
    .popup-message.alert
      background-color clBgPrimaryAlert
      border-left-color clBgSecondaryAlert
    .popup-message._transitionOpacity
      transition opacity 1s ease
      opacity 0.3
    .popup-message._transitionHeight
      transition all 0.3s ease
      height 0
      padding 0
      margin 0
      opacity 0
</style>

<template>
  <div class="popup-messages">
  </div>
</template>

<script lang="ts">
  const DEFAULT_DISSAPPEAR_AFTER_MS = 3000;
  const DEFAULT_TRANSITION_OPACITY_TIME_MS = 500;
  const DEFAULT_TRANSITION_HEIGHT_TIME_MS = 300;

  export default {
    data() {
      return {
        messages: [],
      };
    },

    methods: {
      async __addMessage(title: string, message: string, cls: string, disappearTimeMs: number) {
        const el = document.createElement('div');
        el.classList.add('popup-message', cls);
        el.innerHTML = `<div class="title"><strong>${title}</strong></div>
                        <div class="message">${message}</div>`
        this.$el.append(el);
        el.style.height = Array(el.children).reduce((all, el) => all + el.clientHeight, 0) + 'px';

        setTimeout(() => {
          el.classList.add('_transitionOpacity');
          setTimeout(() => {
            el.classList.add('_transitionHeight');
            el.style.removeProperty('height');
            setTimeout(() => {
              el.remove();
            }, DEFAULT_TRANSITION_HEIGHT_TIME_MS);
          }, DEFAULT_TRANSITION_OPACITY_TIME_MS);
        }, disappearTimeMs);
      },

      success(title: string, message: string = '', timeMs: number = DEFAULT_DISSAPPEAR_AFTER_MS) {
        this.__addMessage(title, message, 'success', timeMs);
      },

      alert(title: string, message: string = '', timeMs: number = DEFAULT_DISSAPPEAR_AFTER_MS) {
        this.__addMessage(title, message, 'alert', timeMs);
      },

      error(title: string, message: string = '', timeMs: number = DEFAULT_DISSAPPEAR_AFTER_MS) {
        this.__addMessage(title, message, 'error', timeMs);
      }
    }
  }
</script>
