<template>
  <div class="page">
    <h4>Thêm mới Liên hệ</h4>
    <ContactForm :contact="newContact" @submit:contact="createContact" />
    <p>{{ message }}</p>
  </div>
</template>

<script>
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

export default {
  components: {
    ContactForm,
  },
  data() {
    return {
      // Khởi tạo một đối tượng liên hệ rỗng để truyền vào ContactForm
      newContact: {
        name: "",
        email: "",
        address: "",
        phone: "",
        favorite: false,
      },
      message: "",
    };
  },
  methods: {
    async createContact(data) {
      try {
        // Gọi API Backend qua Service để tạo mới liên hệ
        await ContactService.create(data);
        alert("Liên hệ mới đã được thêm thành công.");
        // Sau khi thêm thành công, chuyển hướng người dùng về trang chủ
        this.$router.push({ name: "contactbook" });
      } catch (error) {
        console.log(error);
        this.message = "Đã có lỗi xảy ra khi thêm liên hệ mới.";
      }
    },
  },
};
</script>
