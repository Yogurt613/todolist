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
    // 從 localStorage 讀取聯絡人資料，若無則初始化為空陣列
    let savedContacts = localStorage.getItem('contacts'); // 從本地儲存取出聯絡人資料

    // 如果有找到已經保存的聯絡人，就把它轉換成 JavaScript 能讀懂的格式
    if (savedContacts) {
      savedContacts = JSON.parse(savedContacts);
    } else {
      // 如果沒有找到任何已經保存的聯絡人，那就從空白開始
      savedContacts = [];
    }

    // 用 ref 把聯絡人清單包起來，這樣 Vue 才能追蹤它的變化
    const contacts = ref(savedContacts);

    // 搜索查詢
    const searchQuery = ref('');

    // 新聯絡人的名字和電話
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
      // 確保名字和電話欄位不是空的
      if (newContactName.value.trim() !== '' && newContactPhone.value.trim() !== '') {

        // 建立一個新的聯絡人物件
        const newContact = {
          id: contacts.value.length + 1, // 使用陣列長度加1來生成新的ID
          name: newContactName.value.trim(), // 去掉名字前後的空格
          phone: newContactPhone.value.trim(), // 去掉電話前後的空格
          isEditing: false // 設定初始狀態為非編輯狀態
        };

        // 把新聯絡人加入聯絡人陣列中
        contacts.value.push(newContact);

        // 更新 localStorage
        localStorage.setItem('contacts', JSON.stringify(contacts.value));

        // 清空輸入框
        newContactName.value = '';
        newContactPhone.value = '';
      }
    }

    // 編輯聯絡人
    function editContact(contact) {
      contact.isEditing = true;
      contact.editName = contact.name;
      contact.editPhone = contact.phone;
    }

    // 保存編輯後的聯絡人資料
    function saveContact(contact) {
      if (contact.editName.trim() !== '' && contact.editPhone.trim() !== '') {
        contact.name = contact.editName.trim();
        contact.phone = contact.editPhone.trim();
        contact.isEditing = false;

        // 更新 localStorage
        localStorage.setItem('contacts', JSON.stringify(contacts.value));
      }
    }

    // 刪除聯絡人
    function deleteContact(id) {
      // 找到要刪除的聯絡人在陣列中的索引
      const index = contacts.value.findIndex(contact => contact.id === id);

      // 如果找到聯絡人，使用 splice 方法刪除該聯絡人
      if (index !== -1) {
        contacts.value.splice(index, 1);

        // 更新 localStorage 中的聯絡人資料
        localStorage.setItem('contacts', JSON.stringify(contacts.value));
      }
    }

    // 將所有需要在模板中使用的變數和方法返回
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
