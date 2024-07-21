<template>
  <teleport to="body">
    <transition name="modal">
      <div class="modal__wrapper" @click="handleCloseModal">
        <div class="modal-content" @click.stop="">
          <div class="modal-header">
            <span class="modal-title">{{ title }}</span>
          </div>
          <div class="modal-body">
            <slot></slot>
          </div>
        </div>
      </div>
    </transition>
  </teleport>
</template>

<script>
import { useStore } from 'vuex'
export default {
  props: {
    title: {
      type: String,
      default: 'Modal'
    }
  },
  setup() {
    const store = useStore()
    const handleCloseModal = () => {
      store.dispatch('setIsModalContact', false)
      store.dispatch('setIsModalAddContact', false)
    }
    return { handleCloseModal }
  }
}
</script>
