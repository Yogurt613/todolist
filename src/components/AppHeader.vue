<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">聯絡人管理系統</h1>

    <!-- 搜索聯絡人的輸入框 -->
    <div class="input-group mb-3">
      <input v-model="searchQuery" type="text" class="form-control" placeholder="搜尋聯絡人...">
    </div>

    <!-- 新增聯絡人的輸入區域 -->
    <div class="input-group mb-3">
      <input v-model="newContactName" type="text" class="form-control" placeholder="輸入聯絡人名字">
      <input v-model="newContactPhone" type="text" class="form-control" placeholder="輸入聯絡人電話">
      <button @click="addContact" class="btn btn-primary" type="button">新增聯絡人</button>
    </div>

    <!-- 聯絡人列表 -->
    <ul class="list-group">
      <li v-for="contact in filteredContacts" :key="contact.id" class="contact-item">
        <div>
          <!-- 顯示聯絡人信息或編輯表單 -->
          <span v-if="!contact.isEditing">{{ contact.name }} : {{ contact.phone }}</span>
          <div v-else>
            <input v-model="contact.editName" type="text" class="form-control" placeholder="編輯名字">
            <input v-model="contact.editPhone" type="text" class="form-control mt-2" placeholder="編輯電話">
          </div>
        </div>
        <div>
          <!-- 顯示編輯和刪除按鈕 -->
          <button v-if="!contact.isEditing" @click="editContact(contact)" class="btn btn-warning btn-sm">編輯</button>
          <button v-if="contact.isEditing" @click="saveContact(contact)" class="btn btn-success btn-sm">保存</button>
          <button @click="deleteContact(contact.id)" class="btn btn-danger btn-sm ms-2">刪除</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { computed, ref } from 'vue';

export default {
  setup() {
    // 聯絡人的資料
    const contacts = ref([
      { id: 1, name: '張三', phone: '0912345678', isEditing: false, editName: '', editPhone: '' },
      { id: 2, name: '李四', phone: '0987654321', isEditing: false, editName: '', editPhone: '' }
    ]);

    // 搜索查詢
    const searchQuery = ref('');

    // 用於新增聯絡人的名字和電話
    const newContactName = ref('');
    const newContactPhone = ref('');

    // 計算屬性：根據搜索查詢過濾聯絡人
    const filteredContacts = computed(() => {
      const query = searchQuery.value.toLowerCase();
      return contacts.value.filter(contact => 
        contact.name.toLowerCase().includes(query) || 
        contact.phone.includes(query)
      );
    });

    // 新增聯絡人
    function addContact() {
      if (newContactName.value.trim() !== '' && newContactPhone.value.trim() !== '') {
        contacts.value.push({
          id: contacts.value.length ? Math.max(contacts.value.map(c => c.id)) + 1 : 1, // 自動生成唯一 ID
          name: newContactName.value.trim(), // 去除名字前後的空格
          phone: newContactPhone.value.trim(), // 去除電話前後的空格
          isEditing: false, // 初始狀態不在編輯模式
          editName: '',
          editPhone: ''
        });

        // 清空輸入框
        newContactName.value = '';
        newContactPhone.value = '';
      }
    }

    // 編輯聯絡人
    function editContact(contact) {
      // 切換到編輯模式
      contact.isEditing = true;
      // 儲存當前資料作為暫存
      contact.editName = contact.name;
      contact.editPhone = contact.phone;
    }

    // 保存編輯後的聯絡人資料
    function saveContact(contact) {
      if (contact.editName.trim() !== '' && contact.editPhone.trim() !== '') {
        // 更新聯絡人資料
        contact.name = contact.editName.trim();
        contact.phone = contact.editPhone.trim();
        // 退出編輯模式
        contact.isEditing = false;
      }
    }

    // 刪除聯絡人
    function deleteContact(id) {
      contacts.value = contacts.value.filter(contact => contact.id !== id);
    }

    return {
      contacts,
      searchQuery,
      newContactName,
      newContactPhone,
      addContact,
      editContact,
      saveContact,
      deleteContact,
      filteredContacts
    };
  }
};
</script>

<style scoped>
.contact-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  border-bottom: 1px solid #ddd;
}
</style>
