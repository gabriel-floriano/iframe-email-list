<template>
  <div>
    <table>
      <thead>
        <tr>
          <th>
            <input type="checkbox" v-model="allSelected" />
          </th>
          <th>Name</th>
          <th>Email</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="email in emails"
          :key="email.email"
          :class="{ selected: email.selected }"
        >
          <td>
            <input
              type="checkbox"
              :id="email.email"
              :checked="email.selected"
              @change="toggleEmail(email)"
            />
          </td>
          <td>{{ email.name }}</td>
          <td>{{ email.email }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, computed } from "vue";

const emails = ref([]);

const handlePostMessage = (event) => {
  if (
    event.data &&
    event.data.type === "LIST_EMAILS" &&
    Array.isArray(event.data.emails)
  ) {
    emails.value = event.data.emails;
  }
};

const toggleEmail = (email) => {
  email.selected = !email.selected;
  window.parent.postMessage({ type: "TOGGLE_EMAIL", email: { ...email } }, "*");
};

const allSelected = computed({
  get() {
    return (
      emails.value.length > 0 && emails.value.every((email) => email.selected)
    );
  },
  set(value) {
    toggleAllEmails(value);
  },
});

const toggleAllEmails = (newValue) => {
  emails.value.forEach((email) => {
    email.selected = newValue;
    window.parent.postMessage(
      { type: "TOGGLE_EMAIL", email: { ...email } },
      "*"
    );
  });
};

onMounted(() => {
  window.addEventListener("message", handlePostMessage);
});

onBeforeUnmount(() => {
  window.removeEventListener("message", handlePostMessage);
});
</script>

<style>
body {
  margin: 0 !important;
  padding: 0 !important;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  background-color: #f2f2f2;
  text-align: left;
}

tr.selected {
  background-color: #d0e7ff;
}

input[type="checkbox"] {
  margin-right: 10px;
}
</style>
