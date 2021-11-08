<template>
  <div class="dropdown" v-click-outside="hideDropdown">
    <label for="dropdown-input" class="dropdown-label"> Гражданство </label>
    <input
      v-if="Object.keys(selectedCitizenship).length === 0"
      ref="dropdowninput"
      v-model.trim="inputValue"
      class="dropdown-input"
      id="dropdown-input"
      type="text"
      placeholder="Введите страну на латинице"
      @click="showDrpodown"
    />
    <input
      type="text"
      v-else
      @click="resetSelection"
      class="dropdown-input"
      :value="selectedCitizenship.nationality"
    />

    <div v-show="isDropdownOpen" class="dropdown-list">
      <div
        @click="selectCitizenship(item)"
        v-show="itemVisible(item)"
        v-for="item in citizenships"
        :key="item.id"
        class="dropdown-item"
        ref="dropdownitem"
      >
        {{ item.nationality }}
      </div>
    </div>
  </div>
</template>

<script>
import citizenships from '../assets/data/citizenships.json'
import ClickOutside from 'vue-click-outside'
import { debounce } from '../debounce'
export default {
  data() {
    return {
      selectedCitizenship: {},
      inputValue: '',
      citizenships: [],
      isDropdownOpen: false,
      debouncedInput: '',
    }
  },

  directives: {
    ClickOutside,
  },
  created() {
    this.debouncedGetInput = debounce(this.getInputValue, 2000)
  },
  watch: {
    inputValue(newValue) {
      this.debouncedGetInput(newValue)
    },
  },
  mounted() {
    this.getList()
  },
  computed: {},
  methods: {
    showDrpodown() {
      this.isDropdownOpen = true
    },
    hideDropdown(event) {
      if (event.target !== this.$refs.dropdownitem) {
        this.isDropdownOpen = false
      }
    },
    resetSelection() {
      this.selectedCitizenship = {}
      this.$nextTick(() => this.$refs.dropdowninput.focus())
      this.$emit('on-item-reset')
    },
    selectCitizenship(theCitizenship) {
      this.selectedCitizenship = theCitizenship
      this.inputValue = ''
      this.$emit('on-item-selected', theCitizenship)
    },
    itemVisible(item) {
      let currentName = item.nationality.toLowerCase()

      let currentInput = this.debouncedInput.toLowerCase()
      return currentName.includes(currentInput)
    },

    getList() {
      return (this.citizenships = citizenships)
    },
    getInputValue(newValue) {
      this.debouncedInput = newValue
    },
  },
}
</script>

<style>
.dropdown {
  position: relative;
  width: 100%;
  max-width: 400px;
}
.dropdown-input {
  margin-top: 10px;
}
.dropdown-input {
  width: 100%;

  padding: 10px 16px;
  border: 1px solid transparent;
  background: #edf2f7;
  line-height: 1.5em;
  outline: none;
}
.dropdown-input:focus {
  background: #fff;
  border-color: rgb(202, 199, 199);
}
.dropdown-input::placeholder {
  opacity: 0.7;
}

.dropdown-list {
  position: absolute;

  width: 100%;
  max-height: 500px;
  margin-top: 4px;
  overflow-y: auto;
  background: #ffffff;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1),
    0 4px 6px -2px rgba(0, 0, 0, 0.05);
}
.dropdown-item {
  display: flex;
  width: 100%;
  padding: 10px 16px;
  cursor: pointer;
}
.dropdown-item:hover {
  background: #edf2f7;
}
</style>
