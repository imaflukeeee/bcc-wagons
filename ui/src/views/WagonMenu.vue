<template>
  <div class="dashboard-container">
    
    <div v-if="viewMode === 'home'" class="home-menu">
      <div class="showroom-header">
        <h1>{{ shopName || 'WAGON STORE' }}</h1>
        <p>เลือกรายการที่ต้องการ</p>
      </div>

      <div class="menu-grid">
        <div class="menu-card" @click="enterMode('wagons')">
          <div class="card-content">
            <div class="big-icon">
                <img src="img/bgPanel.png" class="white-icon" style="object-fit: cover; border-radius: 50%;" />
            </div>
            <h2>My Wagons</h2>
            <p>จัดการและเรียกใช้งานเกวียนของคุณ</p>
          </div>
        </div>

        <div class="menu-card" @click="enterMode('shop')">
          <div class="card-content">
            <div class="big-icon">
                <img src="img/bgPanel.png" class="white-icon" style="object-fit: cover; border-radius: 50%;" />
            </div>
            <h2>Wagon Shop</h2>
            <p>เลือกซื้อเกวียนรุ่นใหม่</p>
          </div>
        </div>
      </div>
      
      <button class="close-btn-home" @click="close()">ออกจากที่นี่</button>
    </div>


    <div v-else class="content-view">
      
      <button class="floating-back-btn" @click="handleSmartBack">
        <i class="fas fa-chevron-left"></i> {{ subMode === 'list' ? 'HOME' : 'BACK' }} <span style="font-size: 10px; margin-left: 5px; opacity: 0.7;">[BACKSPACE]</span>
      </button>

      <template v-if="viewMode === 'wagons'">
        
        <div v-if="!myWagons || myWagons.length === 0" class="empty-state-view">
            <div class="empty-content">
                <h2>ไม่มีเกวียนในครอบครอง</h2>
                <p>ไปที่ร้านค้าเพื่อเลือกซื้อเกวียนคันแรกของคุณ</p>
                <button class="shop-action-btn btn-white" @click="enterMode('shop')">ไปที่ร้านค้า</button>
            </div>
        </div>

        <template v-else>
            <div v-if="subMode === 'list'" class="carousel-wrapper">
                <button class="carousel-arrow left" @click="prevItem">❮</button>
                
                <div class="carousel-track-container">
                    <div class="carousel-track" :style="trackStyle">
                        <div 
                            v-for="(wagon, index) in myWagons" 
                            :key="index"
                            class="showroom-card-carousel"
                            :class="{ 'active': index === focusedIndex }"
                            @click="selectWagonByIndex(index)"
                        >
                            <div class="card-center-icon">
                                <img src="img/bgPanel.png" class="white-icon-medium" />
                            </div>
                            <div class="card-body">
                                <h3 class="horse-name-show">{{ wagon.name }}</h3>
                                <p class="horse-breed-show">{{ wagon.model }}</p>
                            </div>
                            <div class="selection-border" v-if="index === focusedIndex"></div>
                        </div>
                    </div>
                </div>

                <button class="carousel-arrow right" @click="nextItem">❯</button>
            </div>

            <div v-else-if="subMode === 'detail' && selectedWagon" class="manage-action-view">
                <div class="selected-horse-header">
                    <h1>{{ selectedWagon.name }}</h1>
                    <div class="sub-info">{{ selectedWagon.model }}</div>
                </div>
                
                <div class="manage-stats-wrapper">
                    <WagonStorage 
                        :limit="getWagonLimit(selectedWagon.model)" 
                        :show-action-btn="false" 
                    />
                </div>

                <div class="carousel-wrapper" style="bottom: 120px;">
                    <button class="carousel-arrow left" @click="prevItem">❮</button>
                    
                    <div class="carousel-track-container">
                        <div class="carousel-track" :style="trackStyle">
                            <div 
                                v-for="(action, index) in activeCarouselList" 
                                :key="index"
                                class="showroom-card-carousel"
                                :class="{ 
                                    'active': index === focusedIndex,
                                    'danger-card': action.isDanger 
                                }"
                                @click="selectAction(index)"
                            >
                                <div class="card-center-icon">
                                    <img :src="action.icon" class="white-icon-medium" />
                                </div>
                                <div class="card-body">
                                    <h3 class="horse-name-show" :class="{ 'text-danger': action.isDanger }">{{ action.label }}</h3>
                                    <p class="horse-breed-show">{{ action.subLabel }}</p>
                                </div>
                                <div class="selection-border" v-if="index === focusedIndex"></div>
                            </div>
                        </div>
                    </div>

                    <button class="carousel-arrow right" @click="nextItem">❯</button>
                </div>
            </div>
        </template>
      </template>


      <template v-if="viewMode === 'shop'">
        
        <div v-if="subMode === 'list'" class="shop-list-header">
            <h1>WAGON MARKET</h1>
            <p>เลือกประเภทเกวียน</p>
        </div>

        <div v-if="subMode === 'list'" class="carousel-wrapper">
            <button class="carousel-arrow left" @click="prevItem">❮</button>
            
            <div class="carousel-track-container">
                <div class="carousel-track" :style="trackStyle">
                    <div 
                        v-for="(group, index) in shopList" 
                        :key="index"
                        class="showroom-card-carousel"
                        :class="{ 'active': index === focusedIndex }"
                        @click="selectShopGroup(index)"
                    >
                        <div class="card-center-icon">
                            <img src="img/bgPanel.png" class="white-icon-medium" />
                        </div>
                        <div class="card-body">
                            <h3 class="horse-name-show">{{ group.name }}</h3>
                            <p class="horse-breed-show">{{ Object.keys(group.types).length }} Variants</p>
                        </div>
                        <div class="selection-border" v-if="index === focusedIndex"></div>
                    </div>
                </div>
            </div>

            <button class="carousel-arrow right" @click="nextItem">❯</button>
        </div>

        <div v-else-if="subMode === 'detail' && selectedGroup" class="manage-action-view">
            <div class="selected-horse-header">
                <h1>{{ selectedGroup.name }}</h1>
                <div class="sub-info">
                     Variant: {{ currentVariantData.label }}
                </div>
            </div>

            <div class="manage-stats-wrapper">
                <WagonStorage 
                    :limit="currentVariantData.invLimit || 0" 
                    :show-action-btn="false" 
                />
            </div>

            <div class="shop-controls-center">
                
                <div class="color-selector-row">
                    <button class="nav-arrow arrow-fixed-left" @click="prevVariant">❮</button>
                    <div class="color-info-wrapper">
                        <span class="color-label">VARIANT</span>
                        <span class="color-name">{{ currentVariantData.label }}</span>
                    </div>
                    <button class="nav-arrow arrow-fixed-right" @click="nextVariant">❯</button>
                </div>

                <div class="name-input-wrapper">
                    <input type="text" v-model="newWagonName" placeholder="ตั้งชื่อเกวียน..." class="shop-input" maxlength="20" ref="nameInput" />
                </div>

                <div class="buy-actions-row">
                    <button v-if="currentVariantData.cashPrice > 0" 
                            class="shop-action-btn btn-white" 
                            :disabled="!newWagonName"
                            @click="buyWagon('cash')">
                        <span class="currency-symbol">$</span> {{ currentVariantData.cashPrice }}
                    </button>

                    <button v-if="currentVariantData.goldPrice > 0" 
                            class="shop-action-btn btn-gold" 
                            :disabled="!newWagonName"
                            @click="buyWagon('gold')">
                        <img src="img/gold.png" class="currency-icon"/> {{ currentVariantData.goldPrice }} Gold
                    </button>
                </div>
            </div>
        </div>
      </template>

      <button class="close-btn" @click="close()">✕</button>
    </div>

    <ConfirmationModal :visible="showBuyModal" title="ยืนยันการซื้อ" @close="showBuyModal = false">
      <div style="text-align: center; color: #fff; margin-top: 10px;">
          <p>คุณต้องการซื้อ <strong>{{ currentVariantData.label }}</strong></p>
          <p style="font-size: 14px; opacity: 0.8;">ในราคา</p>
          <h2 v-if="pendingPurchase" style="margin: 5px 0; color: #d4af37;">
              <span v-if="pendingPurchase.currency === 'cash'">$</span>
              {{ pendingPurchase.cost }} 
              <span v-if="pendingPurchase.currency === 'gold'" style="font-size: 0.6em;">GOLD</span>
          </h2>
      </div>
      <div class="divider-menu-top" style="margin-top: 1rem; width: 100%; height: 1px; background: rgba(255,255,255,0.2);"></div>
      <div class="action-bar" style="flex-direction: row; gap: 10px; margin-top: 20px;">
        <button @click="confirmPurchase" class="action-btn btn-white">ยืนยัน</button>
        <button @click="showBuyModal = false" class="action-btn btn-danger">ยกเลิก</button>
      </div>
    </ConfirmationModal>

  </div>
</template>

<script>
import { mapState } from "vuex";
import api from "@/api";
import WagonStorage from "@/components/WagonStorage.vue";
import ConfirmationModal from "@/components/ConfirmationModal.vue";

export default {
  name: "WagonDashboard",
  components: { WagonStorage, ConfirmationModal },
  data() {
    return {
      viewMode: 'home', 
      subMode: 'list',
      focusedIndex: 0,
      selectedWagon: null,
      selectedGroup: null,
      currentVariantIndex: 0,
      newWagonName: "",
      showBuyModal: false,
      pendingPurchase: null,
    };
  },
  computed: {
    ...mapState(["shopName", "myWagons", "wagons"]),

    wagonActions() {
        return [
            { 
                id: 'spawn', 
                label: 'เรียกเกวียน', 
                subLabel: 'Spawn Wagon', 
                icon: 'img/buy-horse-icon.png' 
            },
            { 
                id: 'store', 
                label: 'เก็บเกวียน', 
                subLabel: 'Store Wagon', 
                icon: 'img/x.png',
                isDanger: true
            }
        ];
    },

    shopList() {
        if (!this.wagons) return [];
        return Object.values(this.wagons);
    },

    activeCarouselList() {
        if (this.viewMode === 'wagons') {
            if (this.subMode === 'list') return this.myWagons || [];
            if (this.subMode === 'detail') return this.wagonActions; 
        }
        if (this.viewMode === 'shop') return this.shopList;
        return [];
    },

    // [FIXED] สูตรคำนวณแบบแม่นยำ (Left 50% - Shift)
    trackStyle() {
        const cardWidth = 200; 
        const gap = 20;        
        const totalUnit = cardWidth + gap;
        const centerOffset = cardWidth / 2;
        
        // สูตร: (ตำแหน่ง Index * ความกว้างรวม) + ครึ่งการ์ด
        const shiftAmount = (this.focusedIndex * totalUnit) + centerOffset;
        
        // ลบระยะกลับอย่างเดียว เพราะ CSS ตั้ง left: 50% ไว้แล้ว
        return { transform: `translateX(-${shiftAmount}px)` };
    },

    variantList() {
        if (!this.selectedGroup || !this.selectedGroup.types) return [];
        return Object.entries(this.selectedGroup.types).map(([model, data]) => ({
            model: model,
            ...data
        }));
    },
    currentVariantData() {
        if (this.variantList.length === 0) return {};
        return this.variantList[this.currentVariantIndex];
    }
  },
  
  mounted() {
    window.addEventListener("keydown", this.handleKeydown);
    if (this.viewMode === 'shop' && this.shopList.length > 0) {
         this.previewWagon(this.shopList[0]);
    }
  },
  unmounted() {
    window.removeEventListener("keydown", this.handleKeydown);
  },
  
  watch: {
      viewMode(newVal) {
          this.subMode = 'list';
          this.focusedIndex = 0;
          this.newWagonName = "";
          
          if (document.activeElement) document.activeElement.blur();

          if (newVal === 'shop' && this.shopList.length > 0) {
             this.previewGroup(this.shopList[0]);
          }
      },
      subMode() {
          this.focusedIndex = 0;
      }
  },

  methods: {
    getWagonLimit(modelName) {
        if (!this.wagons) return 0;
        for (const group of Object.values(this.wagons)) {
            if (group.types && group.types[modelName]) {
                 const data = group.types[modelName];
                 if (data.invLimit) return data.invLimit;
                 if (data.inventory && data.inventory.limit) return data.inventory.limit;
                 if (data.limit) return data.limit;
            }
        }
        return 0;
    },

    handleKeydown(e) {
        if (this.showBuyModal) {
            if (e.key === 'Enter') {
                e.preventDefault();
                this.confirmPurchase();
            } else if (e.key === 'Escape') {
                e.preventDefault();
                this.showBuyModal = false;
            }
            return;
        }

        if (document.activeElement === this.$refs.nameInput) {
            if (e.key === 'Enter') {
                e.preventDefault();
                this.buyWagon('cash'); 
            } else if (e.key === 'Escape') {
                e.preventDefault();
                e.stopPropagation();
                document.activeElement.blur();
            }
            return;
        }

        if (e.key === 'Escape') {
            e.preventDefault();
            this.close();
            return;
        }

        if (['ArrowLeft', 'ArrowRight', 'Enter', 'Backspace'].includes(e.key)) {
            e.preventDefault();
            e.stopPropagation();
        }

        if (this.viewMode === 'home') return;

        if (this.subMode === 'list') {
            if (e.key === 'ArrowLeft') this.prevItem();
            else if (e.key === 'ArrowRight') this.nextItem();
            else if (e.key === 'Enter') {
                if (this.viewMode === 'wagons') this.selectWagonByIndex(this.focusedIndex);
                else this.selectShopGroup(this.focusedIndex);
            }
            else if (e.key === 'Backspace') this.goHome(); 
        } 
        else if (this.subMode === 'detail') {
            if (this.viewMode === 'shop') {
                if (e.key === 'ArrowLeft') this.prevVariant();
                else if (e.key === 'ArrowRight') this.nextVariant();
                else if (e.key === 'Enter') this.buyWagon('cash'); 
            }
            else if (this.viewMode === 'wagons') {
                if (e.key === 'ArrowLeft') this.prevItem();
                else if (e.key === 'ArrowRight') this.nextItem();
                else if (e.key === 'Enter') this.selectAction(this.focusedIndex);
            }
            
            if (e.key === 'Backspace') this.handleBack(); 
        }
    },

    handleSmartBack() {
        if (this.subMode === 'detail') {
            this.handleBack(); 
        } else {
            this.goHome(); 
        }
    },

    prevItem() {
        if (this.focusedIndex > 0) {
            this.focusedIndex--;
            this.onScroll();
        }
    },
    nextItem() {
        if (this.focusedIndex < this.activeCarouselList.length - 1) {
            this.focusedIndex++;
            this.onScroll();
        }
    },
    onScroll() {
        if (this.viewMode === 'shop') {
             this.previewGroup(this.activeCarouselList[this.focusedIndex]);
        }
        else if (this.viewMode === 'wagons' && this.subMode === 'list') {
             const w = this.activeCarouselList[this.focusedIndex];
             if(w) this.previewModel(w.model);
        }
    },

    selectWagonByIndex(index) {
        this.focusedIndex = index;
        this.selectedWagon = this.myWagons[index];
        this.subMode = 'detail';
        this.previewModel(this.selectedWagon.model);
    },
    selectShopGroup(index) {
        this.focusedIndex = index;
        this.selectedGroup = this.shopList[index];
        this.currentVariantIndex = 0;
        this.subMode = 'detail';
        this.previewVariant();
        this.$nextTick(() => { if(this.$refs.nameInput) this.$refs.nameInput.focus(); });
    },
    
    selectAction(index) {
        const action = this.wagonActions[index];
        if (action) {
            this.performAction(action.id);
        }
    },

    prevVariant() {
        if (this.currentVariantIndex > 0) this.currentVariantIndex--;
        else this.currentVariantIndex = this.variantList.length - 1;
        this.previewVariant();
    },
    nextVariant() {
        if (this.currentVariantIndex < this.variantList.length - 1) this.currentVariantIndex++;
        else this.currentVariantIndex = 0;
        this.previewVariant();
    },
    previewVariant() {
        if (this.currentVariantData) {
            this.previewModel(this.currentVariantData.model);
        }
    },
    previewGroup(group) {
        if (group && group.types) {
            const firstModel = Object.keys(group.types)[0];
            this.previewModel(firstModel);
        }
    },
    previewModel(modelName) {
        api.post("LoadWagon", { WagonModel: modelName });
    },

    enterMode(mode) {
        this.viewMode = mode;
    },
    goHome() {
        this.viewMode = 'home';
    },
    handleBack() {
        this.subMode = 'list';
    },
    close() {
        api.post("CloseMenu", { MenuAction: "Close" });
    },
    
    buyWagon(currency) {
        if (!this.currentVariantData || !this.newWagonName) return;
        
        this.pendingPurchase = {
            currency: currency,
            cost: (currency === 'cash') ? this.currentVariantData.cashPrice : this.currentVariantData.goldPrice
        };
        this.showBuyModal = true;
    },

    confirmPurchase() {
        if (!this.pendingPurchase) return;
        const isCash = (this.pendingPurchase.currency === 'cash');
        api.post("BuyWagon", {
          ModelW: this.currentVariantData.model,
          Cash: this.currentVariantData.cashPrice,
          Gold: this.currentVariantData.goldPrice,
          IsCash: isCash,
          name: this.newWagonName
        });
        this.showBuyModal = false;
        setTimeout(() => this.close(), 500);
    },

    performAction(action) {
        if (!this.selectedWagon) return;
        if (action === 'spawn') {
             api.post("SpawnWagon", { WagonId: this.selectedWagon.id, WagonModel: this.selectedWagon.model, WagonName: this.selectedWagon.name });
        } else if (action === 'store') {
             api.post("StoreWagon", { WagonId: this.selectedWagon.id });
        }
        setTimeout(() => this.close(), 500);
    }
  },
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Pridi:wght@300;400;500;600;700&display=swap');
* { font-family: 'Pridi', serif !important; }

.dashboard-container { 
    position: fixed; top: 0; left: 0;
    width: 100%; height: 100%; 
    display: flex; color: #f0f0f0; user-select: none;
    overflow: hidden; z-index: 9999;
}

.manage-stats-wrapper {
  position: absolute; 
  top: 45%; 
  left: 80px; 
  transform: translateY(-50%); 
  width: 300px; 
  z-index: 20;
}

.home-menu { width: 100%; height: 100%; display: flex; flex-direction: column; justify-content: center; align-items: center; background: rgba(0,0,0,0.6); }
.showroom-header { text-align: center; margin-bottom: 60px; }
.showroom-header h1 { font-size: 56px; margin: 0; font-weight: 600; text-transform: uppercase; letter-spacing: 4px; color: #fff; }
.showroom-header p { font-size: 16px; color: #888; margin-top: 5px; font-weight: 300; }

.menu-grid { display: flex; gap: 40px; }
.menu-card { 
    width: 320px; height: 280px; 
    background: rgba(22, 22, 22, 0.95); 
    border: 1px solid rgba(255,255,255,0.08); 
    border-radius: 8px; cursor: pointer; 
    display: flex; align-items: center; justify-content: center; 
    transition: all 0.4s; 
}
.menu-card:hover { transform: translateY(-8px); border-color: #d4af37; box-shadow: 0 15px 40px rgba(0,0,0,0.5); }
.card-content { text-align: center; padding: 20px; }
.big-icon { margin-bottom: 20px; }
.white-icon { width: 90px; height: 90px; opacity: 0.8; filter: grayscale(100%); transition: 0.4s; }
.menu-card:hover .white-icon { filter: grayscale(0%); opacity: 1; transform: scale(1.1); }
.menu-card h2 { margin: 0; font-size: 22px; font-weight: 500; color: #e0e0e0; }
.menu-card p { font-size: 13px; color: #666; margin-top: 8px; }

.close-btn-home { margin-top: 60px; padding: 10px 40px; background: transparent; border: 1px solid rgba(255,255,255,0.15); color: #888; border-radius: 30px; cursor: pointer; transition: 0.3s; }
.close-btn-home:hover { border-color: #fff; color: #fff; }

.content-view { width: 100%; height: 100%; position: relative; }

.floating-back-btn { 
    position: absolute; top: 40px; left: 40px; 
    background: rgba(0,0,0,0.5); border: 1px solid rgba(255,255,255,0.2); 
    color: #fff; padding: 10px 20px; border-radius: 30px; 
    cursor: pointer; z-index: 100; transition: 0.3s; 
}
.floating-back-btn:hover { background: #fff; color: #000; }

.close-btn { position: absolute; top: 30px; right: 30px; background: transparent; border: none; color: #666; font-size: 20px; cursor: pointer; z-index: 100; }
.close-btn:hover { color: #fff; transform: rotate(90deg); }

.carousel-wrapper { position: absolute; bottom: 40px; left: 0; width: 100%; display: flex; flex-direction: column; align-items: center; z-index: 10; }
.carousel-track-container { width: 100%; height: 220px; overflow: hidden; position: relative; mask-image: linear-gradient(to right, transparent 0%, black 20%, black 80%, transparent 100%); -webkit-mask-image: linear-gradient(to right, transparent 0%, black 20%, black 80%, transparent 100%); }

/* [FIXED] ใช้ left: 50% ใน CSS เพื่อให้สอดคล้องกับสูตร JS */
.carousel-track { 
    display: flex; gap: 20px; 
    position: absolute; 
    left: 50%; /* จุดเริ่มที่กึ่งกลางจอ */
    top: 50%; 
    transform-origin: 0 0; 
    margin-top: -80px; 
    transition: transform 0.4s cubic-bezier(0.25, 0.8, 0.25, 1); 
    width: max-content; 
}

.showroom-card-carousel { width: 200px; height: 160px; background: rgba(20, 20, 20, 0.8); border: 1px solid rgba(255,255,255,0.1); border-radius: 8px; display: flex; flex-direction: column; justify-content: center; align-items: center; gap: 10px; cursor: pointer; flex-shrink: 0; position: relative; transition: all 0.3s ease; opacity: 0.5; transform: scale(0.9); }
.showroom-card-carousel.active { opacity: 1; transform: scale(1.1); background: rgba(40, 40, 40, 0.95); border-color: #d4af37; box-shadow: 0 10px 30px rgba(0,0,0,0.5); z-index: 2; }
.card-center-icon img { width: 50px; height: 50px; filter: brightness(0) invert(1); opacity: 0.8; }
.card-body { text-align: center; }
.horse-name-show { font-size: 16px; font-weight: 600; margin: 0; }
.horse-breed-show { font-size: 12px; color: #aaa; margin: 0; }
.selection-border { position: absolute; top: -2px; left: -2px; right: -2px; bottom: -2px; border: 2px solid #d4af37; border-radius: 10px; pointer-events: none; }

.danger-card { border-color: #c0392b; background: rgba(192, 57, 43, 0.2); }
.text-danger { color: #e74c3c; }

.carousel-arrow { position: absolute; top: 50%; transform: translateY(-50%); background: rgba(0,0,0,0.5); border: 1px solid rgba(255,255,255,0.2); color: #fff; width: 60px; height: 60px; border-radius: 50%; font-size: 24px; cursor: pointer; z-index: 20; transition: 0.2s; }
.carousel-arrow:hover { background: #fff; color: #000; }
.carousel-arrow.left { left: 10%; }
.carousel-arrow.right { right: 10%; }

.manage-action-view { position: absolute; top: 0; left: 0; width: 100%; height: 100%; display: flex; flex-direction: column; align-items: center; background: linear-gradient(to top, rgba(0,0,0,0.9) 0%, transparent 60%); z-index: 5; }
.selected-horse-header { position: absolute; top: 5%; text-align: center; }
.selected-horse-header h1 { font-size: 48px; margin: 0; letter-spacing: 2px; }
.sub-info { font-size: 16px; color: #ccc; }

.shop-controls-center { position: absolute; bottom: 80px; left: 50%; transform: translateX(-50%); display: flex; flex-direction: column; align-items: center; gap: 20px; width: 100%; z-index: 20; }
.buy-actions-row { display: flex; gap: 15px; }

.shop-action-btn { padding: 10px 25px; border: none; border-radius: 4px; font-weight: 700; cursor: pointer; text-transform: uppercase; display: flex; align-items: center; gap: 8px; min-width: 120px; transition: 0.2s; }
.shop-action-btn:hover { transform: translateY(-2px); }
.shop-action-btn:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }
.btn-white { background: #fff; color: #000; }
.btn-gold { background: #DAA520; color: #000; }
.btn-danger { background: #c0392b; color: #fff; }
.currency-icon { width: 20px; height: 20px; }
.currency-symbol { font-size: 18px; font-weight: 700; color: #111; margin-right: 2px; }

.color-selector-row { display: flex; align-items: center; justify-content: space-between; width: 400px; height: 50px; position: relative; margin-bottom: 10px; }
.nav-arrow { background: rgba(255,255,255,0.1); border: 1px solid rgba(255,255,255,0.2); color: #fff; width: 45px; height: 45px; border-radius: 50%; cursor: pointer; font-size: 18px; display: flex; align-items: center; justify-content: center; transition: 0.2s; }
.nav-arrow:hover { background: #fff; color: #000; }
.arrow-fixed-left { position: absolute; left: 0; top: 50%; transform: translateY(-50%); }
.arrow-fixed-right { position: absolute; right: 0; top: 50%; transform: translateY(-50%); }
.color-info-wrapper { flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: center; overflow: hidden; padding: 0 50px; }
.color-label { font-size: 10px; color: #888; letter-spacing: 2px; }
.color-name { font-size: 24px; font-weight: 600; white-space: nowrap; }

.name-input-wrapper { width: 300px; }
.shop-input { width: 100%; padding: 12px; background: rgba(0,0,0,0.5); border: 1px solid rgba(255,255,255,0.3); color: #fff; text-align: center; font-size: 16px; border-radius: 4px; }
.shop-input:focus { border-color: #d4af37; outline: none; background: rgba(0,0,0,0.7); }

.empty-state-view { position: absolute; width: 100%; height: 100%; display: flex; flex-direction: column; justify-content: center; align-items: center; text-align: center; }
.shop-list-header { position: absolute; top: 10%; width: 100%; text-align: center; pointer-events: none; z-index: 20; }
/* Action Button (Modal) */
.action-btn { width: auto; padding: 10px 25px; border: none; border-radius: 4px; font-family: inherit; font-weight: 600; cursor: pointer; text-transform: uppercase; font-size: 13px; letter-spacing: 1px; transition: all 0.3s ease; }
</style>