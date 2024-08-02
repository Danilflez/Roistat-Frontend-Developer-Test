<template>
  <div>
    <h3>Добавление пользователя</h3>
    <form @submit.prevent="saveUser">
      <div>
        <label>Имя</label>
        <input v-model="name" type="text" required @input="validateName" />
        <span v-if="nameError" class="error">{{ nameError }}</span>
      </div>
      <div>
        <label>Телефон</label>
        <input v-model="phone" type="text" required @input="validatePhone" />
        <span v-if="phoneError" class="error">{{ phoneError }}</span>
      </div>
      <div>
        <label>Начальник</label>
        <select v-model="parentId">
          <option v-for="user in users" :key="user.id" :value="user.id">
            {{ user.name }}
          </option>
        </select>
      </div>
      <button type="submit" :disabled="!!nameError || !!phoneError">
        Сохранить
      </button>
    </form>
  </div>
</template>

<script>
export default {
  props: ["users"],
  data() {
    return {
      name: "",
      phone: "",
      parentId: null,
      nameError: "",
      phoneError: "",
    };
  },
  methods: {
    validateName() {
      const nameRegex = /^[A-Za-zА-Яа-яЁё]+$/;
      if (!nameRegex.test(this.name)) {
        this.nameError =
          "Имя не должно содержать цифры или специальные символы";
      } else {
        this.nameError = "";
      }
    },
    validatePhone() {
      const phoneRegex = /^\+?[1-9]\d{1,14}$/;
      if (!phoneRegex.test(this.phone)) {
        this.phoneError = "Некорректный формат номера телефона";
      } else {
        this.phoneError = "";
      }
    },
    saveUser() {
      if (this.nameError || this.phoneError) {
        return;
      }

      const newUser = {
        id: Date.now(),
        name: this.name,
        phone: this.phone,
        parentId: this.parentId,
        expanded: false,
        children: [],
      };

      if (this.parentId) {
        const parent = this.users.find((user) => user.id === this.parentId);
        parent.children.push(newUser);
      } else {
        this.users.push(newUser);
      }

      localStorage.setItem("users", JSON.stringify(this.users));
      this.$emit("user-saved", newUser);
      this.resetForm();
    },
    resetForm() {
      this.name = "";
      this.phone = "";
      this.parentId = null;
      this.nameError = "";
      this.phoneError = "";
    },
  },
};
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
}
input {
  width: 500px;
}
div {
  margin-bottom: 10px;
}

label {
  margin-bottom: 5px;
}

input,
select {
  padding: 5px;
  border: 1px solid #ccc;
}

button {
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #ccc;
  background-color: #f0f0f0;
  cursor: pointer;
}

button:hover {
  background-color: #e0e0e0;
}

.error {
  color: red;
  font-size: 0.9em;
  margin-top: 5px;
}
</style>
