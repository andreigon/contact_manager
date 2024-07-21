<script>
import ListContacts from '@/components/contacs/ListContacts.vue'
import Notification from '@/components/contacs/Notification.vue'
import { ref, computed } from 'vue'
import { useStore } from 'vuex'

export default {
  components: {
    ListContacts,
    Notification
  },
  setup() {
    const store = useStore()
    //Уведомление о действиях пользователя
    const isNotification = ref(
      computed(() => {
        return store.getters.getIsNotification
      })
    )
    return {
      isNotification
    }
  }
}
</script>

<template>
  <div>
    <ListContacts />
    <Notification v-if="isNotification">{{ isNotification }}</Notification>
  </div>
</template>

