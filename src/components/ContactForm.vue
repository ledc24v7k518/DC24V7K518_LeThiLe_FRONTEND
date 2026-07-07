<template>
  <Form @submit="submitContact" :validation-schema="contactFormSchema">
    <div class="form-group">
      <label for="name">Tên</label>
      <Field
        name="name"
        type="text"
        class="form-control"
        v-model="contactLocal.name"
      />
      <ErrorMessage name="name" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="email">E-mail</label>
      <Field
        name="email"
        type="email"
        class="form-control"
        v-model="contactLocal.email"
      />
      <ErrorMessage name="email" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="address">Địa chỉ</label>
      <Field
        name="address"
        type="text"
        class="form-control"
        v-model="contactLocal.address"
      />
      <ErrorMessage name="address" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="phone">Điện thoại</label>
      <Field
        name="phone"
        type="tel"
        class="form-control"
        v-model="contactLocal.phone"
      />
      <ErrorMessage name="phone" class="error-feedback" />
    </div>

    <div class="form-group form-check">
      <input
        name="favorite"
        type="checkbox"
        class="form-check-input"
        v-model="contactLocal.favorite"
      />
      <label for="favorite" class="form-check-input-label">
        <strong>Liên hệ yêu thích</strong>
      </label>
    </div>
    <div class="form-group">
      <label>Bạn có sở thích không?</label>
      <div>
        <div class="form-check form-check-inline">
          <Field
            name="hasHobbies"
            type="radio"
            value="yes"
            class="form-check-input"
            v-model="contactLocal.hasHobbies"
          />
          <label class="form-check-label">Có</label>
        </div>
        <div class="form-check form-check-inline">
          <Field
            name="hasHobbies"
            type="radio"
            value="no"
            class="form-check-input"
            v-model="contactLocal.hasHobbies"
          />
          <label class="form-check-label">Không</label>
        </div>
      </div>
    </div>

    <div class="form-group ml-3" v-if="contactLocal.hasHobbies === 'yes'">
      <label>Chọn các sở thích của bạn (Chọn ít nhất 1):</label>
      <div>
        <div class="form-check">
          <Field
            name="hobbies"
            type="checkbox"
            value="Xã Hội"
            class="form-check-input"
            v-model="contactLocal.hobbies"
          />
          <label class="form-check-label">Xã Hội</label>
        </div>
        <div class="form-check">
          <Field
            name="hobbies"
            type="checkbox"
            value="Khoa Học"
            class="form-check-input"
            v-model="contactLocal.hobbies"
          />
          <label class="form-check-label">Khoa Học</label>
        </div>
        <div class="form-check">
          <Field
            name="hobbies"
            type="checkbox"
            value="Công Nghệ"
            class="form-check-input"
            v-model="contactLocal.hobbies"
          />
          <label class="form-check-label">Công Nghệ</label>
        </div>
        <div class="form-check">
          <Field
            name="hobbies"
            type="checkbox"
            value="Giải Trí"
            class="form-check-input"
            v-model="contactLocal.hobbies"
          />
          <label class="form-check-label">Giải Trí</label>
        </div>
      </div>
      <ErrorMessage name="hobbies" class="error-feedback" />
    </div>
    <div class="form-group">
      <button class="btn btn-primary"><i class="fas fa-save"></i> Lưu</button>
      <button
        v-if="contactLocal._id"
        type="button"
        class="ml-2 btn btn-danger"
        @click="deleteContact"
      >
        <i class="fas fa-trash"></i> Xóa
      </button>
    </div>
  </Form>
</template>

<script>
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";

export default {
  components: {
    Form,
    Field,
    ErrorMessage,
  },
  emits: ["submit:contact", "delete:contact"],
  props: {
    contact: { type: Object, required: true },
  },
  data() {
    const contactFormSchema = yup.object().shape({
      name: yup
        .string()
        .required("Tên phải có giá trị.")
        .min(2, "Tên phải ít nhất 2 ký tự.")
        .max(50, "Tên có nhiều nhất 50 ký tự."),
      email: yup
        .string()
        .email("E-mail không đúng.")
        .max(50, "E-mail tối đa 50 ký tự."),
      address: yup.string().max(100, "Địa chỉ tối đa 100 ký tự."),
      phone: yup
        .string()
        .matches(
          /((09|03|07|08|05)+([0-9]{8})\b)/g,
          "Số điện thoại không hợp lệ.",
        ),
      // --- Cập nhật Logic Ràng Buộc ---
      hasHobbies: yup.string().nullable(),
      hobbies: yup.array().when("hasHobbies", {
        is: "yes",
        then: (schema) => schema.min(1, "Bạn phải chọn ít nhất 1 sở thích."),
        otherwise: (schema) => schema.notRequired(),
      }),
    });

    return {
      // Đảm bảo dữ liệu nhận vào form có sẵn mảng rỗng và giá trị mặc định
      // cho sở thích nếu chưa có
      contactLocal: {
        hasHobbies: this.contact.hasHobbies || "no",
        hobbies: this.contact.hobbies || [],
        ...this.contact,
      },
      contactFormSchema,
    };
  },
  methods: {
    submitContact() {
      // Trước khi gửi đi, nếu chọn "Không" thì dọn sạch mảng sở thích 
      // tránh lưu rác dữ liệu
      if (this.contactLocal.hasHobbies === "no") {
        this.contactLocal.hobbies = [];
      }
      this.$emit("submit:contact", this.contactLocal);
    },
    deleteContact() {
      this.$emit("delete:contact", this.contactLocal._id);
    },
  },
};
</script>
