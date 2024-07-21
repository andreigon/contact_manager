<script>
import SelectCategory from '@/components/UI/SelectCategory.vue'
import LoadSpinner from '@/components/UI/LoadSpinner.vue'
import useVuelidate from '@vuelidate/core'
import {
  helpers,
  required,
  email,
  minLength,
  maxLength
} from '@vuelidate/validators'
import { useIMask } from 'vue-imask'
import { ref, computed, reactive, onMounted } from 'vue'
import { useStore } from 'vuex'
export default {
  components: {
    SelectCategory,
    LoadSpinner
  },
  props: {
    name: {
      type: String,
      default: ''
    },
    phone: {
      type: String,
      default: ''
    },
    email: {
      type: String,
      default: ''
    },
    date: {
      type: String,
      default: ''
    },
    category: {
      type: String,
      default: ''
    }
  },
  setup(props) {
    const store = useStore()
    onMounted(() => {
      initializationName()
    })
    const initializationName = () => {
      store.dispatch('setNameContact', props.name)
    }
    //Массив для select
    const dataSelect = [
      {
        value: 'relatives',
        text: 'Семья'
      },
      {
        value: 'colleagues',
        text: 'Работа'
      }
    ]
    //Открыто/закрыто модальное окно
    const isModalContact = ref(
      computed(() => {
        return store.getters.getIsModalContact
      })
    )
    //Первоначальное заполнение контакта
    const contact = reactive({
      name: props.name,
      category: props.category,
      date: props.date,
      phone: props.phone,
      email: props.email
    })
    const handleSelected = selectedCategory => {
      contact.category = selectedCategory
    }
              //ВАЛИДАЦИЯ ФОРМ
    const { el, masked } = useIMask({
      mask: '+{7}(000)000-00-00'
    })
    const requiredWithMessage = helpers.withMessage(
      'Поле не может быть пустым',
      required
    )
    const minLengthWithMessage = helpers.withMessage(
      'Слишком короткое имя',
      minLength(3)
    )
    const minLengthPhoneWithMessage = helpers.withMessage(
      'Некорректный номер',
      minLength(16)
    )
    const maxLengthPhoneWithMessage = helpers.withMessage('', maxLength(16))

    const emailWithMessage = helpers.withMessage('Некорректный e-mail', email)
    const rules = computed(() => {
      return {
        name: { requiredWithMessage, minLengthValue: minLengthWithMessage },
        category: { requiredWithMessage },

        phone: {
          requiredWithMessage,
          minLengthPhoneWithMessage,
          maxLengthPhoneWithMessage
        },
        email: { requiredWithMessage, emailWithMessage }
      }
    })

    const v$ = useVuelidate(rules, contact)
    const errorsNameLength = ref(
      computed(() => {
        return v$.value.name.$errors.length
      })
    )
    const errorsPhoneLength = ref(
      computed(() => {
        return v$.value.phone.$errors.length
      })
    )
    const errorsEmailLength = ref(
      computed(() => {
        return v$.value.email.$errors.length
      })
    )
    const errorsCategoryLength = ref(
      computed(() => {
        return v$.value.category.$errors.length
      })
    )
    //Отправка нового/измененного контакта в store
    const isLoading = ref(false)
    const onSubmit = () => {
      v$.value.$validate()
      if (v$.value.$errors.length === 0) {
        isLoading.value = true
        setTimeout(() => {
          const newContact = { ...contact }
          store.dispatch('setData', newContact)
          contact.name = ''
          contact.category = ''
          contact.date = ''
          contact.phone = ''
          contact.email = ''
          store.dispatch('setIsModalContact', false)
          store.dispatch('setIsModalAddContact', false)
          isLoading.value = false
        }, 2000)
      }
    }
    //Отправка удаляемого контакта в store
    const handleDeleteContact = () => {
      isLoading.value = true
      setTimeout(() => {
        store.dispatch('setDeleteContact', contact)
        store.dispatch('setIsModalContact', false)
        store.dispatch('setIsModalAddContact', false)
        isLoading.value = false
      }, 2000)
    }
    return {
      onSubmit,
      handleDeleteContact,
      handleSelected,
      contact,
      v$,
      errorsNameLength,
      errorsPhoneLength,
      errorsEmailLength,
      errorsCategoryLength,
      el,
      dataSelect,
      isModalContact,
      isLoading
    }
  }
}
</script>

<template>
  <form @submit.prevent="">
    <div class="forma">
      <div class="headers">
        <label
          class="headers-label"
          :class="{ label__error: v$.name.$errors.length != 0 }"
          for="name"
          >Имя</label
        >
        <label
          class="headers-label"
          :class="{ label__error: v$.phone.$errors.length != 0 }"
          for="phone"
          >Телефон</label
        >
        <label
          class="headers-label"
          :class="{ label__error: v$.email.$errors.length != 0 }"
          for="email"
          >E-mail</label
        >
        <label
          class="headers-label"
          :class="{ label__error: v$.category.$errors.length != 0 }"
          for="category"
          >Категория</label
        >
        <label class="headers-label" v-if="isModalContact">Изменен</label>
      </div>
      <div class="input-fields">
        <input
          class="forma__input"
          :class="{ error: errorsNameLength != 0 }"
          v-model="contact.name"
          type="text"
          id="name"
          placeholder="Например «Андрей»..."
        />
        <span
          class="forma-errors errors__name"
          v-for="error of v$.name.$errors"
          :key="error.$uid"
        >
          {{ error.$message }}
        </span>

        <input
          class="forma__input"
          :class="{ error: errorsPhoneLength != 0 }"
          v-model="contact.phone"
          type="phone"
          id="phone"
          placeholder="+7(___)___-__-__"
          ref="el"
        />
        <span
          class="forma-errors errors__phone"
          v-for="error of v$.phone.$errors"
          :key="error.$uid"
        >
          {{ error.$message }}
        </span>

        <input
          class="forma__input"
          :class="{ error: errorsEmailLength != 0 }"
          v-model="contact.email"
          type="email"
          id="email"
          placeholder="Например «pochta@damain.ru»..."
        />
        <span
          class="forma-errors errors__email"
          v-for="error of v$.email.$errors"
          :key="error.$uid"
        >
          {{ error.$message }}
        </span>

        <SelectCategory
          class="forma__select"
          :class="{ error: errorsCategoryLength != 0 }"
          id="category"
          @onSelected="handleSelected"
          :category="contact.category"
          :data="dataSelect"
        ></SelectCategory>
        <span
          class="forma-errors errors__category"
          v-for="error of v$.category.$errors"
          :key="error.$uid"
        >
          {{ error.$message }}
        </span>

        <p v-if="isModalContact">{{ date }}</p>
        <div class="btns">
          <button class="btn btnSave" @click="onSubmit" type="submit">
            Сохранить
          </button>
          <button
            v-if="isModalContact"
            class="btn btnDelete"
            @click="handleDeleteContact"
          >
            Удалить контакт
          </button>
        </div>
      </div>
    </div>
    <div class="for-small-screen">
      <button class="btn btnSave" @click="onSubmit" type="submit">
        Сохранить
      </button>
      <button
        v-if="isModalContact"
        class="btn btnDelete"
        @click="handleDeleteContact"
      >
        Удалить контакт
      </button>
    </div>
  </form>
  <LoadSpinner v-if="isLoading"></LoadSpinner>
</template>

<style lang="scss">
.error {
  border: 2px solid #eb5757 !important;
  color: #eb5757 !important;
}
.label__error {
  display: inline-block;
  position: relative;
  &:before {
    content: '';
    background-image: url('@/assets/image/IconError.svg');
    background-repeat: no-repeat;
    background-size: cover;
    height: 13px;
    width: 13px;
    display: block;
    position: absolute;
    left: 540px;
    z-index: 1;
    top: 13px;
    font-size: 23px;
    color: rgb(21, 19, 19);
    @media screen and (max-width: 720px) {
      left: 420px;
    }
    @media screen and (max-width: 560px) {
      left: 290px;
    }
    @media screen and (max-width: 376px) {
      left: 280px;
    }
  }
}
</style>

