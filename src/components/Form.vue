<template>
  <form @submit="sendForm">
    <div class="person">
      <h3>Личные данные</h3>
      <div class="person-name">
        <label>
          Фамилия
          <input
            type="text"
            name="surname"
            id="surname"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </label>

        <label>
          Имя
          <input
            type="text"
            name="name"
            id="name"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </label>

        <label>
          Отчество
          <input
            type="text"
            name="middle-name"
            id="middle-name"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </label>
      </div>

      <div>
        <label>
          Дата рождения
          <input
            type="date"
            name="birthday"
            id="birthday"
            placeholder="дд.мм.гггг"
            min="1900-01-01"
            :max="formatedDate"
          />
        </label>
      </div>

      <div>
        <label>
          E-mail
          <input type="email" name="email" id="email" />
        </label>
      </div>

      <div>
        <p>Пол</p>
        <div class="radio">
          <div>
            <input type="radio" id="male" name="gender" value="male" checked />
            <label for="male">Мужской</label>
          </div>

          <div>
            <input type="radio" id="female" name="gender" value="female" />
            <label for="female">Женский</label>
          </div>
        </div>
      </div>
    </div>
    <div class="passport">
      <h3>Паспортные данные</h3>
      <div>
        <Dropdown
          @on-item-selected="citizenship = $event.nationality"
          @on-item-reset="citizenship = ''"
        />
      </div>

      <div v-if="isRussia">
        <label>
          Серия паспорта
          <input
            type="text"
            name="passport-series"
            id="series"
            title="Введите 4 цифры"
            pattern="[0-9]{4}"
          />
        </label>
        <label>
          Номер паспорта
          <input
            type="text"
            name="passport-number"
            id="number"
            title="Введите 6 цифр"
            pattern="[0-9]{6}"
          />
        </label>
        <label>
          Дата выдачи
          <input
            type="date"
            name="passport-date"
            id="passport"
            placeholder="дд.мм.гггг"
          />
        </label>
      </div>

      <div class="foreign" v-else>
        <div>
          <label>
            Фамилия на латинице
            <input
              type="text"
              name="surname-latin"
              id="surname-latin"
              title="Разрешено использовать только пробелы и латинские буквы"
              pattern="^[a-zA-Z\s]+$"
            />
          </label>
          <label>
            Имя на латинице
            <input
              type="text"
              name="name-latin"
              id="name-latin"
              title="Разрешено использовать только пробелы и латинские буквы"
              pattern="^[a-zA-Z\s]+$"
            />
          </label>
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
          <label>
            Страна выдачи
            <select name="passport-country" v-model="passportCountry">
              <option
                v-for="(citizenship, i) in citizenships"
                :key="i"
                :value="citizenship.nationality"
              >
                {{ citizenship.nationality }}
              </option>
            </select>
          </label>
        </div>

        <div>
          <label
            >Тип паспорта
            <select name="passport-type">
              <option
                v-for="(passport, i) in passportTypes"
                :key="i"
                :value="passport.type"
              >
                {{ passport.type }}
              </option>
            </select>
          </label>
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

      <div v-if="isChanged === 'yes'">
        <label>
          Предыдущая Фамилия
          <input
            type="text"
            name="surname"
            id="surname"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </label>
        <label>
          Предыдущее Имя
          <input
            type="text"
            name="name"
            id="name"
            title="Разрешено использовать только пробелы и русские буквы"
            pattern="^[А-Яа-яЁё\s]+$"
          />
        </label>
      </div>
    </div>

    <button @click="checkValidity" type="submit">Отправить</button>
  </form>
</template>

<script>
import citizenships from '../assets/data/citizenships.json'
import passportTypes from '../assets/data/passport-types.json'
import ClickOutside from 'vue-click-outside'
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

  directives: {
    ClickOutside,
  },

  methods: {
    sendForm(event) {
      event.preventDefault()
      console.log(event.target.validity)
      const data = new FormData(event.target)
      const dataArr = Array.from(data).filter((item) => !!item[1])
      const dataObj = JSON.stringify(Object.fromEntries(dataArr))

      console.log(dataObj)
    },

    search(searchWord) {
      console.log('FETCH SKILLS EVENT: GET SKILLS FROM API', searchWord)
    },
    checkValidity() {
      this.$el.querySelectorAll('input').forEach((input) => {
        if (!input.validity.valid) {
          input.style.border = '1px solid red'
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
  watch: {
    citizenship(newValue) {
      this.search(newValue)

      return (this.passportCountry = this.citizenship)
    },
  },
}
</script>

<style scoped>
form {
  padding: 50px;
  box-shadow: rgba(99, 99, 99, 0.2) 0px 2px 8px 0px;
}
h3 {
  margin-bottom: 30px;
}
.person {
  border-bottom: 1px solid rgb(185, 184, 184);
  margin-bottom: 40px;
}
.person > div,
.passport > div {
  margin-bottom: 30px;
}
input {
  margin-left: 10px;
  padding: 4px;
  outline: none;
  border: 1px solid rgb(202, 199, 199);
}
input[type='email'] {
  width: 400px;
}

label {
  margin-right: 20px;
}
.radio {
  display: flex;
}
select {
  padding: 5px;
  border: 1px solid rgb(202, 199, 199);
}
.foreign > div {
  margin-bottom: 30px;
}
.foreign select {
  margin-left: 10px;
}
button {
  padding: 10px;
  background: rgb(27, 139, 231);
  color: #fff;
  border: none;
  border-radius: 5px;
  box-shadow: rgba(50, 50, 93, 0.25) 0px 6px 12px -2px,
    rgba(0, 0, 0, 0.3) 0px 3px 7px -3px;
  text-shadow: 2px 4px 3px rgba(0, 0, 0, 0.3);
}
button:hover {
  cursor: pointer;
  background: rgb(9, 130, 230);
}
</style>
