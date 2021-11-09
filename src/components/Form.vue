<template>
  <form @submit.prevent="sendForm" @keyup.enter="checkValidity" class="form">
    <div class="person">
      <h3 class="form-title">Личные данные</h3>
      <div class="name">
        <div>
          <label for="surname"> Фамилия</label>
          <input
            type="text"
            name="surname"
            id="surname"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </div>

        <div>
          <label for="name"> Имя </label>
          <input
            type="text"
            name="name"
            id="name"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </div>

        <div>
          <label for="middle-name"> Отчество </label>
          <input
            type="text"
            name="middle-name"
            id="middle-name"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </div>
      </div>

      <div>
        <label for="birthday"> Дата рождения </label>
        <input
          type="date"
          name="birthday"
          id="birthday"
          placeholder="дд.мм.гггг"
          min="1900-01-01"
          :max="formatedDate"
        />
      </div>

      <div>
        <label for="email"> E-mail</label>
        <input type="email" name="email" id="email" />
      </div>

      <div>
        <p>Пол</p>
        <div class="radio">
          <div>
            <input type="radio" id="male" name="gender" value="male" checked />
            <label for="male">Мужской</label>
          </div>
          <div>
            <input
              type="radio"
              id="female"
              name="gender"
              value="female"
              required
            />
            <label for="female">Женский</label>
          </div>
        </div>
      </div>
    </div>

    <div class="passport">
      <h3 class="form-title">Паспортные данные</h3>

      <Dropdown
        @on-item-selected="citizenship = $event.nationality"
        @on-item-reset="citizenship = ''"
        :input-name="'citizenship-country'"
        :dropdown-id="'citizenship-country'"
      >
        Гражданство
      </Dropdown>

      <div class="russian" v-if="isRussia">
        <div>
          <label for="passport-series"> Серия паспорта</label>
          <input
            type="text"
            name="passport-series"
            id="passport-series"
            title="Введите 4 цифры"
            pattern="[0-9]{4}"
          />
        </div>

        <div>
          <label for="passport-number"> Номер паспорта</label>
          <input
            type="text"
            name="passport-number"
            id="passport-number"
            title="Введите 6 цифр"
            pattern="[0-9]{6}"
          />
        </div>

        <div>
          <label for="passport-date"> Дата выдачи </label>
          <input
            type="date"
            name="passport-date"
            id="passport-date"
            placeholder="дд.мм.гггг"
            min="1900-01-01"
            :max="formatedDate"
          />
        </div>
      </div>

      <div class="foreign" v-else>
        <div class="name">
          <div>
            <label for="surname-latin"> Фамилия на латинице</label>
            <input
              type="text"
              name="surname-latin"
              id="surname-latin"
              title="Разрешено использовать только пробелы и латинские буквы"
              pattern="^[a-zA-Z\s]+$"
            />
          </div>

          <div>
            <label for="name-latin"> Имя на латинице </label>
            <input
              type="text"
              name="name-latin"
              id="name-latin"
              title="Разрешено использовать только пробелы и латинские буквы"
              pattern="^[a-zA-Z\s]+$"
            />
          </div>
        </div>

        <div>
          <label>
            Номер паспорта
            <input
              type="number"
              name="foreign-passport-number"
              id="foreign-passport-number"
            />
          </label>
        </div>

        <Dropdown
          @on-item-selected="passportCountry = $event.nationality"
          @on-item-reset="passportCountry = ''"
          :input-name="'passport-country'"
          :dropdown-id="'passport-country'"
          >Страна выдачи</Dropdown
        >

        <div class="passport-type">
          <label for="passport-type">Тип паспорта </label>
          <select id="passport-type" name="passport-type">
            <option
              v-for="(passport, i) in passportTypes"
              :key="i"
              :value="passport.type"
            >
              {{ passport.type }}
            </option>
          </select>
        </div>
      </div>

      <div>
        <p>Меняли ли фамилию или имя?</p>
        <div class="radio">
          <div>
            <input
              type="radio"
              id="yes"
              name="change-name"
              value="yes"
              v-model="isChanged"
            />
            <label for="yes">Да</label>
          </div>

          <div>
            <input
              type="radio"
              id="no"
              name="change-name"
              value="no"
              v-model="isChanged"
            />
            <label for="no">Нет</label>
          </div>
        </div>
      </div>

      <div v-if="isChanged === 'yes'" class="name">
        <div>
          <label for="previous-surname"> Предыдущая Фамилия </label>
          <input
            type="text"
            name="previous-surname"
            id="previous-surname"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </div>

        <div>
          <label for="previous-name"> Предыдущее Имя </label>
          <input
            type="text"
            name="previous-name"
            id="previous-name"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </div>
      </div>
    </div>

    <button @click="checkValidity" type="submit" class="submit-button">
      Отправить
    </button>
  </form>
</template>

<script>
import citizenships from '../assets/data/citizenships.json'
import passportTypes from '../assets/data/passport-types.json'
import { format } from 'date-fns'
import Dropdown from './Dropdown.vue'

export default {
  components: {
    Dropdown,
  },
  data() {
    return {
      citizenships: citizenships,
      citizenship: 'Russia',
      passportTypes: passportTypes,
      isChanged: 'no',
      passportCountry: '',
    }
  },

  methods: {
    sendForm(event) {
      const data = new FormData(event.target)
      const dataArr = Array.from(data).filter((item) => !!item[1])
      const dataObj = JSON.stringify(Object.fromEntries(dataArr))
      console.log(dataObj)
    },

    checkValidity() {
      this.$el.querySelectorAll('input').forEach((input) => {
        if (!input.validity.valid) {
          if (!input.classList.contains('invalid')) {
            input.classList.add('invalid')
          }
        } else {
          if (input.classList.contains('invalid')) {
            input.classList.remove('invalid')
          }
        }
      })
    },
  },

  computed: {
    isRussia() {
      return this.citizenship === 'Russia'
    },
    formatedDate() {
      return format(new Date(), 'yyyy-MM-dd')
    },
  },
}
</script>

<style scoped>
.form {
  padding: 50px;
  box-shadow: rgba(99, 99, 99, 0.2) 0px 2px 8px 0px;
}
.form-title {
  margin-bottom: 30px;
}
.person {
  border-bottom: 1px solid rgb(185, 184, 184);
  margin-bottom: 40px;
}
.name {
  display: flex;
}
.name div {
  margin-right: 20px;
}
.person > div,
.passport > div {
  margin-bottom: 30px;
}
.passport-type label {
  margin-right: 10px;
}
.passport-type select {
  padding: 5px;
}
.form input {
  margin-left: 10px;
  padding: 4px;
  outline: none;
  border: 1px solid rgb(202, 199, 199);
}
.form input[type='email'] {
  width: 400px;
}
.radio {
  display: flex;
}
.foreign > div,
.russian > div {
  margin-bottom: 30px;
}
.submit-button {
  padding: 10px;
  background: rgb(27, 139, 231);
  color: #fff;
  border: none;
  border-radius: 5px;
  box-shadow: rgba(50, 50, 93, 0.25) 0px 6px 12px -2px,
    rgba(0, 0, 0, 0.3) 0px 3px 7px -3px;
  text-shadow: 2px 4px 3px rgba(0, 0, 0, 0.3);
}
.submit-button:hover {
  cursor: pointer;
  background: rgb(9, 130, 230);
}
.invalid {
  border: 1px solid red !important;
}
</style>
