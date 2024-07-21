<script>
import { useStore } from 'vuex'
export default {
  setup() {
    //Закрытие уведомления через 2с
    const store = useStore()
    setTimeout(() => {
      store.dispatch('setIsNotification', '')
    }, 2000)
  }
}
</script>

<template>
  <div class="notification__wrapper">
    <div class="notification">
      <img
        class="notification_img"
        src="@/assets/image/IconNotification.svg"
        alt="icon"
      />
      <div class="notification_text">
        <span> Контакт успешно</span><slot></slot>
      </div>
    </div>
  </div>
</template>

