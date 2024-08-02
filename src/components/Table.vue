<template>
  <div class="container">
    <button class="add-button" @click="openModal">Добавить</button>
    <table class="user-table">
      <thead>
        <tr>
          <th @click="sortBy('name')">Имя</th>
          <th @click="sortBy('phone')">Телефон</th>
          <th>Действия</th>
        </tr>
      </thead>
      <tbody>
        <template v-for="user in sortedUsers">
          <tr :key="user.id">
            <td @click="toggle(user)">
              <span
                v-if="user.children && user.children.length"
                @click.stop="toggle(user)"
              >
                <span v-if="user.expanded">-</span>
                <span v-else>+</span>
              </span>
              {{ user.name }}
            </td>
            <td>{{ user.phone }}</td>
            <td><button @click.stop="removeUser(user)">Удалить</button></td>
          </tr>
          <tr
            v-if="user.expanded"
            v-for="child in user.children"
            :key="child.id"
          >
            <td :style="{ paddingLeft: '20px' }" colspan="3">
              <div>
                <span
                  v-if="child.children && child.children.length"
                  @click.stop="toggle(child)"
                >
                  <span v-if="child.expanded">-</span>
                  <span v-else>+</span>
                </span>
                {{ child.name }} - {{ child.phone }}
                <button @click.stop="removeUser(child)">Удалить</button>
              </div>
              <template v-if="child.expanded">
                <div
                  v-for="grandchild in child.children"
                  :key="grandchild.id"
                  :style="{ paddingLeft: '20px' }"
                >
                  {{ grandchild.name }} - {{ grandchild.phone }}
                  <button @click.stop="removeUser(grandchild)">Удалить</button>
                </div>
              </template>
            </td>
          </tr>
        </template>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: JSON.parse(localStorage.getItem("users")) || [],
      sortKey: "name",
      sortOrders: {
        name: 1,
        phone: 1,
      },
    };
  },
  computed: {
    sortedUsers() {
      return this.users.sort((a, b) => {
        let result = 0;
        if (a[this.sortKey] > b[this.sortKey]) {
          result = 1;
        } else if (a[this.sortKey] < b[this.sortKey]) {
          result = -1;
        }
        return result * this.sortOrders[this.sortKey];
      });
    },
  },
  methods: {
    openModal() {
      this.$emit("open-modal");
    },
    sortBy(key) {
      this.sortOrders[key] = this.sortOrders[key] * -1;
      this.sortKey = key;
    },
    toggle(user) {
      user.expanded = !user.expanded;
    },
    removeUser(user) {
      this.deleteUser(this.users, user);
      localStorage.setItem("users", JSON.stringify(this.users));
    },
    deleteUser(users, userToDelete) {
      const index = users.findIndex((user) => user.id === userToDelete.id);
      if (index !== -1) {
        users.splice(index, 1);
        return true;
      }
      return users.some((user) =>
        this.deleteUser(user.children || [], userToDelete)
      );
    },
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.add-button {
  background-color: #f0f0f0;
  border: 1px solid #ccc;
  border-radius: 10px;
  padding: 10px 20px;
  margin-bottom: 20px;
  cursor: pointer;
}

.add-button:hover {
  background-color: #e0e0e0;
}

.user-table {
  width: 50%;
  border-collapse: collapse;
}

.user-table th,
.user-table td {
  border: 1px solid #ccc;
  padding: 10px;
  text-align: left;
}

.user-table th {
  background-color: #f0f0f0;
  cursor: pointer;
}

.user-table th:hover {
  background-color: #e0e0e0;
}
</style>
