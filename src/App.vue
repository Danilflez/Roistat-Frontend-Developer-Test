<template>
  <div id="app">
    <Table :users="users" @open-modal="showModal = true" />
    <Modal :isVisible="showModal" @close="showModal = false">
      <Form :users="users" @user-saved="addUser" />
    </Modal>
  </div>
</template>

<script>
import Table from './components/Table.vue';
import Modal from './components/Modal.vue';
import Form from './components/Form.vue';

export default {
  components: {
    Table,
    Modal,
    Form,
  },
  data() {
    return {
      showModal: false,
      users: JSON.parse(localStorage.getItem('users')) || [],
    };
  },
  methods: {
    addUser(newUser) {
      this.users.push(newUser);
      localStorage.setItem('users', JSON.stringify(this.users));
      this.showModal = false;
    },
  },
};
</script>

<style>
</style>