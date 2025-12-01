<template>
  <div class="stats-container-large">
    <div class="stats-header">
      <h2>WAGON CART</h2>
      <div class="header-line"></div>
    </div>
    
    <div class="stats-list">
      <div class="stat-item">
        
        <div class="stat-top-row">
           <div class="stat-numbers-wrapper center-align">
              <span class="val-label">ความจุ</span>
              <span class="val-max">{{ limit }}</span>
              <span class="val-unit">ช่อง</span>
           </div>
        </div>

        <button v-if="showActionBtn" class="action-button-small" @click="$emit('open-cargo')">
          เปิดกระเป๋า
        </button>

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "WagonStorage",
  props: {
    limit: {
      type: [Number, String],
      default: 0
    },
    showActionBtn: {
      type: Boolean,
      default: false
    }
  }
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Pridi:wght@300;400;500;600;700&display=swap');

.stats-container-large {
  width: 100%;
  padding: 0;
  background: transparent;
  border: none;
  box-shadow: none;
  font-family: 'Pridi', serif;
}

.stats-header h2 {
  margin: 0;
  font-size: 28px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 2px;
  text-align: center;
  /* สีทอง */
  background: linear-gradient(to bottom, #f1c40f, #b7950b);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  color: #f1c40f;
}

.header-line {
  width: 70px;
  height: 3px;
  background: #ffffff;
  margin: 10px auto 22px auto;
  border-radius: 2px;
}

.stats-list {
  display: flex;
  flex-direction: column;
  gap: 18px;
}

.stat-item {
  display: flex;
  flex-direction: column;
  gap: 6px;
  align-items: center;
}

.stat-top-row {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 2px;
}

.stat-numbers-wrapper {
  display: flex;
  align-items: baseline;
  gap: 8px;
  font-family: 'Pridi', serif;
}

.center-align {
  justify-content: center;
  width: 100%;
}

.val-label, .val-max, .val-unit {
  font-size: 20px;
  font-weight: 500;
  color: #ffffff;
}

.val-unit {
  font-size: 16px;
  opacity: 0.8;
}

.action-button-small {
    padding: 8px 25px;
    background: #d4af37;
    color: #111;
    border: none;
    border-radius: 20px;
    font-size: 14px;
    font-weight: 700;
    text-transform: uppercase;
    cursor: pointer;
    margin-top: 10px; 
}
</style>