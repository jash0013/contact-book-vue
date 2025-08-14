<template>
  <div class="container">
    <h1>Contact Book</h1>

    <!-- Home Button: always visible except on list view -->
    <div v-if="currentView !== 'list'">
      <button @click="goList" class="btn btn-home">Home</button>
    </div>

    <!-- List View -->
    <div v-if="currentView === 'list'">
      <input v-model="search" placeholder="Search contacts..." class="input"/>
      <button @click="goAdd" class="btn">Add Contact</button>
      <ul class="contact-list">
        <li v-for="contact in filteredContacts" :key="contact.id">
          <a @click="goDetails(contact.id)">
            {{ contact.firstName }} {{ contact.lastName }}
          </a>
        </li>
      </ul>
    </div>

    <!-- Contact Details View -->
    <div v-else-if="currentView === 'details' && contact">
      <div class="card">
        <h2>{{ contact.firstName }} {{ contact.lastName }}</h2>
        <p><strong>Email:</strong> {{ contact.email }}</p>
        <button @click="goEdit(contact.id)" class="btn">Edit</button>
        <button @click="deleteContact(contact.id)" class="btn btn-delete">Delete</button>
      </div>
    </div>

    <!-- Add Contact View -->
    <div v-else-if="currentView === 'add'">
      <div class="card">
        <h2>Add Contact</h2>
        <form @submit.prevent="addContact">
          <input v-model="form.firstName" placeholder="First Name" required class="input"/>
          <input v-model="form.lastName" placeholder="Last Name" required class="input"/>
          <input v-model="form.email" placeholder="Email" required class="input"/>
          <button type="submit" class="btn">Add</button>
        </form>
      </div>
    </div>

    <!-- Edit Contact View -->
    <div v-else-if="currentView === 'edit' && contact">
      <div class="card">
        <h2>Edit Contact</h2>
        <form @submit.prevent="updateContact">
          <input v-model="form.firstName" placeholder="First Name" required class="input"/>
          <input v-model="form.lastName" placeholder="Last Name" required class="input"/>
          <input v-model="form.email" placeholder="Email" required class="input"/>
          <button type="submit" class="btn">Update</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      contacts: JSON.parse(localStorage.getItem('contacts') || '[]'),
      search: '',
      currentView: 'list',
      contact: null,
      form: { firstName: '', lastName: '', email: '' },
    };
  },
  computed: {
    filteredContacts() {
      return this.contacts
        .filter(c =>
          c.firstName.toLowerCase().includes(this.search.toLowerCase()) ||
          c.lastName.toLowerCase().includes(this.search.toLowerCase())
        )
        .sort((a, b) => a.lastName.localeCompare(b.lastName));
    },
  },
  methods: {
    saveContacts() {
      localStorage.setItem('contacts', JSON.stringify(this.contacts));
    },
    goList() {
      this.currentView = 'list';
      this.contact = null;
      this.form = { firstName: '', lastName: '', email: '' };
    },
    goDetails(id) {
      this.contact = this.contacts.find(c => c.id === id);
      this.currentView = 'details';
    },
    goAdd() {
      this.form = { firstName: '', lastName: '', email: '' };
      this.currentView = 'add';
    },
    goEdit(id) {
      this.contact = this.contacts.find(c => c.id === id);
      this.form = { ...this.contact };
      this.currentView = 'edit';
    },
    deleteContact(id) {
      this.contacts = this.contacts.filter(c => c.id !== id);
      this.saveContacts();
      this.goList();
    },
    addContact() {
      const newContact = { ...this.form, id: '_' + Math.random().toString(36).substr(2, 9) };
      this.contacts.push(newContact);
      this.saveContacts();
      this.goDetails(newContact.id);
    },
    updateContact() {
      const index = this.contacts.findIndex(c => c.id === this.contact.id);
      this.contacts[index] = { ...this.form, id: this.contact.id };
      this.saveContacts();
      this.goDetails(this.contact.id);
    },
  },
};
</script>

<style>
.container {
  width: 400px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
}
.input {
  width: 100%;
  padding: 6px;
  margin: 5px 0;
}
.btn {
  padding: 6px 12px;
  margin: 5px 2px;
  cursor: pointer;
}
.btn-home {
  background-color: #6c757d;
  color: white;
}
.btn-home:hover {
  background-color: #5a6268;
}
.btn-delete {
  background-color: #dc3545;
  color: white;
}
.btn-delete:hover {
  background-color: #c82333;
}
.card {
  border: 1px solid #ddd;
  padding: 10px;
  margin-top: 10px;
}
.contact-list li {
  margin: 5px 0;
  list-style: none;
}
.contact-list a {
  cursor: pointer;
  color: #007bff;
  text-decoration: none;
}
.contact-list a:hover {
  text-decoration: underline;
}
</style>
