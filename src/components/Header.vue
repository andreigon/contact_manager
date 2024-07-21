<script>
import { useStore } from 'vuex'
import { ref, computed } from 'vue'
export default {
  setup() {
    const store = useStore()
    const isModalAddContact = ref(
      computed(() => {
        return store.getters.getIsModalAddContact
      })
    )
    const isModalContact = ref(
      computed(() => {
        return store.getters.getIsModalContact
      })
    )
    const handleCloseModal = () => {
      store.dispatch('setIsModalContact', false)
      store.dispatch('setIsModalAddContact', false)
    }
    const name = ref(
      computed(() => {
        return store.getters.getNameContact
      })
    )
    const firstLetterName = ref(
      computed(() => {
        return name.value[0]
      })
    )
    return {
      isModalAddContact,
      isModalContact,
      handleCloseModal,
      name,
      firstLetterName
    }
  }
}
</script>

<template>
  <header>
    <div class="navbar navbar__header">
      <div class="navbar-content">
        <div class="navbar-items">
          <div
            v-show="!isModalAddContact && !isModalContact"
            class="navbar-title"
          >
            CONTACT MANAGER
          </div>
          <div v-show="isModalAddContact || isModalContact"></div>
          <div v-show="isModalAddContact" class="navbar-title-add">
            <img src="@/assets/image/IconAddContact.svg" alt="Icon add" />
            <span>New Contact</span>
          </div>
          <div v-show="isModalContact" class="navbar-title-delete">
            <img class="navbar-title-circle" src="@/assets/image/EditProfile.svg" alt="Edit">
            <span> Edit a contact: </span>
            <!-- <span class="navbar-title-circle">{{ firstLetterName }}</span> -->
            <span>{{ name }}</span>
          </div>
          <div
            v-show="isModalAddContact || isModalContact"
            class="button-close"
            @click="handleCloseModal"
          >
            <img src="@/assets/image/IconClose.svg" alt="Button close" />
          </div>
        </div>
      </div>
    </div>
  </header>
</template>

