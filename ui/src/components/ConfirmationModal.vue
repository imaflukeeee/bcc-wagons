<template>
  <div class="modal-wrapper" v-if="visible">
    <div class="modal-overlay" @click="close"></div>
    <div class="modal-container">
      <div class="modal-header">
        <h3 class="modal-title">{{ title }}</h3>
      </div>
      <div class="modal-content">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ConfirmationModal",
  props: {
    visible: {
      type: Boolean,
      required: true,
    },
    title: {
      type: String,
      default: "Confirmation",
    },
  },
  emits: ['close'],
  methods: {
    close() {
      this.$emit("close");
    },
  },
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Pridi:wght@300;400;500;600;700&display=swap');

.modal-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 10000; /* เพิ่ม z-index ให้สูงกว่า Dashboard */
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7); /* พื้นหลังดำจางๆ เข้มขึ้น */
  z-index: -1;
}

.modal-container {
  position: relative;
  width: 400px;
  max-width: 90%;
  background: rgba(20, 20, 20, 0.95); /* ปรับความทึบให้เห็นตัวหนังสือชัด */
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 6px;
  box-shadow: 0 15px 40px rgba(0, 0, 0, 0.8);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  font-family: 'Pridi', serif;
  animation: fadeIn 0.2s ease-out;
}

.modal-header {
  padding: 15px 20px;
  background: rgba(255, 255, 255, 0.03);
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  text-align: center;
}

.modal-title {
  margin: 0;
  font-size: 22px; /* ปรับขนาดให้พอดี */
  font-weight: 600;
  color: #fff;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.modal-content {
  padding: 25px 20px;
  color: #e0e0e0;
  font-size: 14px;
  font-weight: 300;
  line-height: 1.6;
  display: flex;        /* เพิ่ม Flex เพื่อจัดเนื้อหา */
  flex-direction: column;
  align-items: center;  /* จัดกึ่งกลางแนวนอน */
}

@keyframes fadeIn {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}
</style>