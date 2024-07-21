<script>
import NavigationBar from '@/components/NavigationBar.vue'
import Modal from '@/components/UI/Modal.vue'
import ContactForm from '@/components/contacs/ContactForm.vue'
import ContactItemRow from '@/components/contacs/ContactItemRow.vue'
import { ref, computed, onMounted } from 'vue'
import { useStore } from 'vuex'

export default {
  components: {
    ContactItemRow,
    Modal,
    ContactForm,
    NavigationBar
  },
  setup() {
    const title = 'Контакт'
    //Получение данных из localStorage
    const store = useStore()
    const initializationData = () => {
      store.dispatch('initializationData')
    }
    onMounted(() => {
      initializationData()
    })
    //Получение данных контактов
    const data = ref(
      computed(() => {
        return store.getters.getDataSorted
      })
    )

    //Данные для модального окна
    const name = ref('')
    const phone = ref('')
    const email = ref('')
    const date = ref('')
    const category = ref('')
    const handleOpenModal = element => {
      store.dispatch('setIsModalContact', true)
      const contact = { ...element }
      name.value = contact.name
      phone.value = contact.phone
      email.value = contact.email
      date.value = contact.date
      category.value = contact.category
    }
    //Открыто/закрыто модальное окно
    const isModalContact = ref(
      computed(() => {
        return store.getters.getIsModalContact
      })
    )
    return {
      data,
      handleOpenModal,
      isModalContact,
      title,
      name,
      phone,
      email,
      date,
      category
    }
  }
}
</script>

<template>
  <NavigationBar />
  <div class="container">
    <div class="contacts-list">
      <div class="contacts-list__wrapper">
        <div class="contact-name">Контакт</div>
        <div class="phone-email">
          <div class="contact-phone">Телефон</div>
          <div class="contact-email">E-mail</div>
        </div>
        <div class="contact-date">Изменен</div>
      </div>
      <ContactItemRow
        @click="handleOpenModal(element)"
        :contact="element"
        v-for="(element, index) in data"
        :key="index"
      />
    </div>
  </div>
  <Modal v-if="isModalContact" :title="title">
    <ContactForm
      :name="name"
      :phone="phone"
      :email="email"
      :date="date"
      :category="category"
    />
  </Modal>
</template>

