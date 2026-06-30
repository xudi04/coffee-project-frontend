<template>
  <section class="branches-section">
    <div class="container">
      
      <!-- Üst Başlık Grubu -->
      <div class="section-header">
        <span class="section-subtitle">İSTANBUL'DA KAHVE NOKTALARIMIZ</span>
        <h2 class="section-title">Şubelerimizin Sıcaklığını Hissedin</h2>
      </div>

      <!-- Şubeler Grid Yapısı -->
      <div class="branches-grid">
        <div 
          v-for="branch in branches" 
          :key="branch.id" 
          class="branch-card"
        >
          <img :src="branch.image" :alt="branch.name" class="branch-img" />
          <div class="branch-overlay"></div>
          
          <!-- Kart İçeriği (Tam Ortada) -->
          <div class="branch-content">
            <h3 class="branch-name">{{ branch.name }}</h3>
            <p class="branch-desc">{{ branch.shortDesc }}</p>
          </div>
          
          <!-- Alt Butonlar -->
          <div class="branch-footer">
            <a :href="branch.mapUrl" target="_blank" class="branch-btn left-btn">Haritada Gör ↗</a>
            <!-- Şube Detayları Tetikleyici Buton -->
            <button @click="openModal(branch)" class="branch-btn right-btn">Şube Detayları</button>
          </div>
        </div>
      </div>

    </div>

    <!-- INTERAKTIF POP-UP (MODAL) BİLEŞENİ -->
    <Teleport to="body">
      <!-- Arka Plan Karartması (Overlay) -->
      <div 
        class="modal-backdrop" 
        :class="{ 'is-visible': isModalOpen }" 
        @click="closeModal"
      >
        <!-- Pop-up Kutusu (Modal Box) -->
        <!-- @click.stop mantığı, kutunun içine tıklayınca modalın kapanmasını engeller -->
        <div 
          class="modal-box" 
          :class="{ 'is-open': isModalOpen }" 
          @click.stop
        >
          <!-- Kapatma Butonu (X) -->
          <button class="modal-close-btn" @click="closeModal" aria-label="Kapat">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
          </button>

          <!-- İçerik Alanı -->
          <div v-if="selectedBranch" class="modal-body">
            <h3 class="modal-branch-name">{{ selectedBranch.name }}</h3>
            
            <div class="modal-details-list">
              <!-- Çalışma Saatleri -->
              <div class="detail-item">
                <div class="detail-icon">🕒</div>
                <div class="detail-text">
                  <strong>Çalışma Saatleri</strong>
                  <span>Hafta içi: {{ selectedBranch.workingHours }}</span>
                  <!-- <span>Hafta sonu: {{ selectedBranch.workingHours }}</span> -->
                </div>
              </div>

              <!-- Açık Adres -->
              <div class="detail-item">
                <div class="detail-icon">📍</div>
                <div class="detail-text">
                  <strong>Açık Adres</strong>
                  <span>{{ selectedBranch.address }}</span>
                </div>
              </div>

              <!-- İletişim Numarası -->
              <div class="detail-item">
                <div class="detail-icon">📞</div>
                <div class="detail-text">
                  <strong>İletişim</strong>
                  <span>{{ selectedBranch.phone }}</span>
                </div>
              </div>

              <!-- Sunulan Hizmetler -->
              <div class="detail-item">
                <div class="detail-icon">☕</div>
                <div class="detail-text">
                  <strong>Sunulan Hizmetler</strong>
                  <span>{{ selectedBranch.services }}</span>
                </div>
              </div>
            </div>

            <!-- Geniş Harita Butonu -->
            <a :href="selectedBranch.mapUrl" target="_blank" class="modal-map-btn">
              Haritada Gör ↗
            </a>
          </div>
        </div>
      </div>
    </Teleport>
  </section>
</template>

<script setup>
import { ref,onMounted } from 'vue'

// --- REAKTİF STATE YÖNETİMİ ---
const isModalOpen = ref(false)
const selectedBranch = ref(null)

// --- POP-UP FONKSİYONLARI ---
const openModal = (branch) => {
  selectedBranch.value = branch
  console.log(branch);
  
  isModalOpen.value = true
  // Modal açıldığında arkadaki sayfanın kaymasını engelleme (UX Güvencesi)
  document.body.style.overflow = 'hidden'
}

const closeModal = () => {
  isModalOpen.value = false
  // Modal kapandığında sayfa kaymasını tekrar açıyoruz
  document.body.style.overflow = ''
}

// --- STATİK ŞUBE VERİ MODELİ ---
const branches = ref([])

const STRAPI_BASE_URL = 'http://localhost:1337'

const fetchBranches = async () => {
  try {
    // Görsel elementini de çekebilmek için populate=* parametresiyle istek atıyoruz
    const response = await fetch(`${STRAPI_BASE_URL}/api/branches?populate=*`)
    if (!response.ok) throw new Error(`HTTP hata: ${response.status}`)
    
    const json = await response.json()
    const rawData = json.data || []
    
    branches.value = rawData.map(item => {
      const fields = item.attributes ? item.attributes : item
      
      // Panelden yeni eklediğin image elementinin derin düğüm kontrolü ve URL birleştirmesi
      const imgData = fields.image?.data?.attributes || fields.image
      const imageUrl = imgData?.url ? `${STRAPI_BASE_URL}${imgData.url}` : 'https://via.placeholder.com/600x400?text=Tariks+Coffee+Branch'

      return {
        id: item.id,
        name: fields.name,
        address: fields.address,
        workingHours: fields.workingHours,
        phone: fields.phone,
        mapUrl: fields.mapUrl,
        image: imageUrl // Şablonunda (template) <img :src="branch.image" /> olarak doğrudan kullanabilirsin
      }
    })
  } catch (error) {
    console.error("Strapi'den şubeler ve görselleri yüklenirken hata oluştu:", error)
  }
}

onMounted(() => {
  fetchBranches()
})
</script>

<style scoped>
/* --- TEMEL ŞUBELER ALANI STİLLERİ --- */
.branches-section {
  width: 100%;
  background-color: var(--bg-antrasit);
  padding: 100px 0;
  color: #ffffff;
}

.section-header {
  text-align: center;
  margin-bottom: 64px;
}

.section-subtitle {
  font-family: var(--font-sans);
  font-size: 0.9rem;
  font-weight: 600;
  color: var(--color-gold);
  letter-spacing: 0.15em;
  display: block;
  margin-bottom: 16px;
}

.section-title {
  font-family: var(--font-serif);
  font-size: 2.5rem;
  font-weight: 500;
}

.branches-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 32px;
}

.branch-card {
  position: relative;
  overflow: hidden;
  aspect-ratio: 3 / 4.2;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  box-shadow: 0 16px 40px rgba(0, 0, 0, 0.4);
  transition: var(--transition-smooth);
}

.branch-card:hover {
  transform: translateY(-4px);
}

.branch-img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: 0;
  transition: var(--transition-smooth);
}

.branch-card:hover .branch-img {
  transform: scale(1.05);
}

.branch-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(30, 31, 34, 0.75);
  z-index: 1;
}

.branch-content {
  position: relative;
  z-index: 2;
  padding: 0 32px;
  text-align: center;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.branch-name {
  font-family: var(--font-serif);
  font-size: 1.6rem;
  font-weight: 500;
  line-height: 1.3;
}

.branch-desc {
  font-family: var(--font-sans);
  font-size: 0.9rem;
  font-weight: 300;
  color: rgba(255, 255, 255, 0.75);
  line-height: 1.6;
}

.branch-footer {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  display: flex;
  z-index: 2;
}

.branch-btn {
  flex: 1;
  background-color: #E2BA73;
  color: #ffffff;
  font-family: var(--font-sans);
  font-size: 0.85rem;
  font-weight: 600;
  text-align: center;
  padding: 16px 0;
  text-decoration: none;
  border: none;
  cursor: pointer;
  transition: var(--transition-smooth);
}

.left-btn {
  border-right: 1px solid rgba(255, 255, 255, 0.25);
  display: flex;
  justify-content: center;
  align-items: center;
}

.branch-btn:hover {
  background-color: var(--color-gold);
  color: var(--bg-antrasit);
}

/* ==========================================================================
   INTERAKTIF POP-UP (MODAL) PREMIUM CSS MIMARISI
   ========================================================================== */

/* 1. Arka Plan Karartması (Overlay) */
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(30, 31, 34, 0); /* Başlangıçta şeffaf */
  z-index: 9999;
  display: flex;
  justify-content: center;
  align-items: center;
  pointer-events: none;
  backdrop-filter: blur(0px);
  transition: background-color 0.4s ease, backdrop-filter 0.4s ease;
}

.modal-backdrop.is-visible {
  background-color: rgba(30, 31, 34, 0.8); /* %80 Opaklıkta Dark Antrasit */
  pointer-events: auto;
  backdrop-filter: blur(4px);
}

/* 2. Pop-up Kutusu (Masaüstü Temel Yapı) */
.modal-box {
  background-color: #2C221E; /* Zengin Dark Espresso */
  width: 100%;
  max-width: 550px;
  padding: 40px;
  box-shadow: 0 24px 64px rgba(0, 0, 0, 0.6);
  position: relative;
  border: 1px solid rgba(226, 186, 115, 0.15); /* İnce gold çerçeve */
  
  /* Giriş Efekti: Hafif aşağıdan gelip şeffaflıktan görünürlüğe geçiş */
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.4s cubic-bezier(0.16, 1, 0.3, 1), transform 0.4s cubic-bezier(0.16, 1, 0.3, 1);
}

.modal-box.is-open {
  opacity: 1;
  transform: translateY(0);
}

/* Kapatma Butonu (X) */
.modal-close-btn {
  position: absolute;
  top: 24px;
  right: 24px;
  background: none;
  border: none;
  color: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  transition: var(--transition-smooth);
}

.modal-close-btn:hover {
  color: var(--color-gold);
  transform: rotate(90px);
}

/* 3. Modal İçi Tipografi ve Düzen */
.modal-branch-name {
  font-family: var(--font-serif);
  font-size: 2rem;
  color: var(--color-gold);
  margin-bottom: 32px;
  font-weight: 500;
  line-height: 1.2;
}

.modal-details-list {
  display: flex;
  flex-direction: column;
  gap: 24px;
  margin-bottom: 40px;
}

.detail-item {
  display: flex;
  gap: 16px;
  align-items: flex-start;
}

.detail-icon {
  font-size: 1.2rem;
  margin-top: 2px;
}

.detail-text {
  display: flex;
  flex-direction: column;
  gap: 4px;
  font-family: var(--font-sans);
}

.detail-text strong {
  font-size: 0.95rem;
  color: #ffffff;
  letter-spacing: 0.05em;
  text-transform: uppercase;
}

.detail-text span {
  font-size: 0.9rem;
  color: rgba(255, 255, 255, 0.75);
  line-height: 1.5;
}

/* En Alttaki Büyük Geniş Harita Butonu */
.modal-map-btn {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  background-color: var(--color-gold);
  color: var(--bg-antrasit);
  font-family: var(--font-sans);
  font-size: 1rem;
  font-weight: 600;
  padding: 16px 0;
  text-decoration: none;
  text-align: center;
  transition: var(--transition-smooth);
}

.modal-map-btn:hover {
  background-color: #ffffff;
  color: var(--bg-antrasit);
}

/* ==========================================================================
   RESPONSIVE DESIGN & MOBILE BOTTOM SHEET KANUNLARI (max-width: 768px)
   ========================================================================== */

@media (max-width: 1024px) {
  .branches-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }
}

@media (max-width: 768px) {
  .section-title { font-size: 2rem; }
  
  .branches-grid {
    grid-template-columns: 1fr;
    max-width: 380px;
    margin: 0 auto;
  }
  
  /* --- MOBILDE BOTTOM SHEET TARZI AŞAĞIDAN YUKARI AÇILMA --- */
  .modal-backdrop {
    align-items: flex-end; /* Kutuyu ekranın en altına yaslar */
  }

  .modal-box {
    max-width: 100%;
    border-radius: 24px 24px 0 0; /* Sadece üst köşeler ovalleşir */
    padding: 40px 24px 32px 24px; /* Padding mobilde daraltıldı */
    border-left: none;
    border-right: none;
    border-bottom: none;
    
    /* Mobilde aşağıdan yukarı kayarak gelme efekti */
    transform: translateY(100%);
    transition: transform 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  }

  .modal-box.is-open {
    transform: translateY(0);
  }

  .modal-close-btn {
    top: 20px;
    right: 20px;
  }

  .modal-branch-name {
    font-size: 1.6rem;
    margin-bottom: 24px;
  }

  .modal-details-list {
    gap: 20px;
    margin-bottom: 32px;
  }

  .detail-text span {
    font-size: 0.85rem;
  }
}
</style>