<script>
import { ref, computed } from 'vue'
export default {
  components: {},
  props: {
    contact: {
      type: Object,
      default: {}
    }
  },
  setup(props) {
    const firstLetterName = ref(
      computed(() => {
        return props.contact.name[0]
      })
    )
    return {
      firstLetterName
    }
  }
}
</script>

<template>
  <div class="contacts-list__wrapper row-data">
    <div class="name-data">
      <span class="contact-name-circle">{{ firstLetterName }}</span>
      <span>{{ contact.name }}</span>
    </div>
    <div class="phone-email">
      <div class="phone-data">{{ contact.phone }}</div>
      <div class="email-data">{{ contact.email }}</div>
    </div>

    <div class="date-data">{{ contact.date }}</div>
  </div>
</template>

