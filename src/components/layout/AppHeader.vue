<!-- src/components/layout/AppHeader.vue -->
<template>
  <header class="app-header">
    <div class="container header-container">
      <!-- Logo Alanı (Arama çubuğu masaüstünde açıkken de görünür, mobilde gizlenir) -->
      <div v-if="!isSearchOpen || !isMobile" class="logo">
        <a href="#">TARIK'S COFFEE ROASTERS</a>
      </div>
      <!-- Masaüstü Navigasyon Menüsü (Arama Açıkken Gizlenir) -->
      <nav v-if="!isSearchOpen" class="nav-menu desktop-menu">
        <a href="#hero" class="nav-link" :class="{ active: activeSection === 'hero' }"
          @click="scrollToSection($event, 'hero')">Ana Sayfa</a>
        <a href="#about" class="nav-link" :class="{ active: activeSection === 'about' }"
          @click="scrollToSection($event, 'about')">Hikayemiz</a>
        <a href="#coffees" class="nav-link" :class="{ active: activeSection === 'coffees' }"
          @click="scrollToSection($event, 'coffees')">Kahvelerimiz</a>
        <a href="#branches" class="nav-link" :class="{ active: activeSection === 'branches' }"
          @click="scrollToSection($event, 'branches')">Şubelerimiz</a>
      </nav>
      <!-- AKTİVİTE 2: PREMIUM INSTANT SEARCH BAR -->
      <div v-if="isSearchOpen" class="instant-search-wrapper">
        <input v-model="searchText" type="text" placeholder="Kahve adı veya kökeni arayın..." class="search-input"
          @input="emitSearch" ref="searchInputRef" />
        <button class="search-close-inline" @click="closeSearch" aria-label="Aramayı Kapat">✕</button>
      </div>
      <!-- Sağ Eylem Alanı -->
      <div class="header-actions">
        <!-- Arama Tetikleyici Büyüteç Butonu -->
        <button v-if="!isSearchOpen" class="action-trigger-btn" @click="openSearch" aria-label="Arama Yap">
          <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 24 24" fill="none"
            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="11" cy="11" r="8"></circle>
            <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
          </svg>
        </button>
        <!-- Sepet Butonu -->
        <button class="cart-btn" @click="isCartOpen = true" aria-label="Sepetim">
          <svg xmlns="http://www.w3.org/2000/svg" width="26" height="26" viewBox="0 0 24 24" fill="none"
            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="8" cy="21" r="1"></circle>
            <circle cx="19" cy="21" r="1"></circle>
            <path d="M2.05 2.05h2l2.66 12.42a2 2 0 0 0 2 1.58h9.78a2 2 0 0 0 1.95-1.57l1.65-7.43H5.12"></path>
          </svg>
          <span v-if="totalItemsCount > 0" class="cart-badge">{{ totalItemsCount }}</span>
        </button>
        <!-- Mobil Menü Butonu -->
        <button class="hamburger-btn" :class="{ 'is-active': isMenuOpen }" @click="toggleMenu" aria-label="Menü">
          <span class="bar"></span><span class="bar"></span><span class="bar"></span>
        </button>
      </div>
    </div>
    <!-- Mobil Yandan Açılan Menü -->
    <div class="mobile-sidebar" :class="{ 'open': isMenuOpen }">
      <nav class="mobile-nav-links">
        <a href="#hero" class="mobile-link" :class="{ active: activeSection === 'hero' }"
          @click="scrollToSection($event, 'hero')">Ana Sayfa</a>
        <a href="#about" class="mobile-link" :class="{ active: activeSection === 'about' }"
          @click="scrollToSection($event, 'about')">Hikayemiz</a>
        <a href="#coffees" class="mobile-link" :class="{ active: activeSection === 'coffees' }"
          @click="scrollToSection($event, 'coffees')">Kahvelerimiz</a>
        <a href="#branches" class="mobile-link" :class="{ active: activeSection === 'branches' }"
          @click="scrollToSection($event, 'branches')">Şubelerimiz</a>
      </nav>
    </div>
    <div class="sidebar-overlay" :class="{ 'visible': isMenuOpen }" @click="closeMenu"></div>
    <!-- YANDAN AÇILAN SEPET PANELİ -->
    <Teleport to="body">
      <div class="cart-backdrop" :class="{ 'is-visible': isCartOpen }" @click="isCartOpen = false">
        <div class="cart-panel" :class="{ 'is-open': isCartOpen }" @click.stop>
          <div class="cart-panel-header">
            <h3 class="cart-panel-title">Alışveriş Sepetim ({{ totalItemsCount }})</h3>
            <button class="cart-close-btn" @click="isCartOpen = false" aria-label="Sepeti Kapat">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none"
                stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="18" y1="6" x2="6" y2="18"></line>
                <line x1="6" y1="6" x2="18" y2="18"></line>
              </svg>
            </button>
          </div>
          <!-- Sepet İçerik Gövdesi -->
          <div class="cart-panel-body">
            <div v-if="showCheckoutModal" class="checkout-modal-overlay"
              style="position: fixed; top: 0; left: 0; width: 100%; height: 100%; background-color: rgba(0, 0, 0, 0.5); display: flex; justify-content: center; align-items: center; z-index: 10000; padding: 20px;">
              <div class="checkout-modal-box"
                style="background-color: #fff; padding: 30px; border-radius: 12px; width: 100%; max-width: 500px; box-shadow: 0 10px 25px rgba(0,0,0,0.2); position: relative;">
                <button @click="showCheckoutModal = false"
                  style="position: absolute; top: 15px; right: 15px; border: none; background: none; font-size: 20px; cursor: pointer; color: #777;">✕</button>
                <h3 style="margin-top: 0; margin-bottom: 25px; color: #333; text-align: center;">Teslimat Bilgileri</h3>
                <div style="display: flex; flex-direction: column; gap: 15px; margin-bottom: 25px;">
                  <input v-model="customerName" type="text" placeholder="Adınız Soyadınız"
                    style="padding: 12px; border: 1px solid #ddd; border-radius: 6px; font-size: 15px;" required />
                  <input v-model="customerEmail" type="email" placeholder="E-posta Adresiniz"
                    style="padding: 12px; border: 1px solid #ddd; border-radius: 6px; font-size: 15px;" required />
                  <textarea v-model="shippingAddress" placeholder="Teslimat Adresi" rows="4"
                    style="padding: 12px; border: 1px solid #ddd; border-radius: 6px; font-size: 15px; resize: none;"
                    required></textarea>
                </div>
                <button @click="proceedToIyzicoPayment"
                  style="width: 100%; padding: 14px; background-color: #f7a01b; color: #fff; border: none; border-radius: 8px; font-size: 16px; font-weight: bold; cursor: pointer; transition: background-color 0.2s;"
                  onmouseover="this.style.backgroundColor='#e69519'" onmouseout="this.style.backgroundColor='#f7a01b'">
                  Bilgileri Kaydet ve Ödemeye Geç </button>
              </div>
            </div>
            <div v-if="cartItems.length === 0" class="empty-cart-message">
              <div class="empty-icon">☕</div>
              <p>Sepetiniz şu an boş.</p>
            </div>
            <div v-else class="cart-items-list">
              <div v-for="item in cartItems" :key="item.cartId" class="cart-item-card">
                <img :src="item.image" :alt="item.name" class="cart-item-img" />
                <div class="cart-item-info">
                  <h4 class="cart-item-name">{{ item.name }}</h4>
                  <span class="cart-item-grind">Öğütme: {{ item.selectedGrind }}</span>
                  <span class="cart-item-price">{{ item.price }} TL</span>
                  <div class="quantity-control">
                    <button @click="updateQuantity(item.cartId, -1)" class="qty-btn" aria-label="Azalt">-</button>
                    <span class="qty-value">{{ item.quantity }}</span>
                    <button @click="updateQuantity(item.cartId, 1)" class="qty-btn" aria-label="Artır">+</button>
                  </div>
                </div>
                <button @click="removeFromCart(item.cartId)" class="cart-item-remove-btn" aria-label="Ürünü Sil">
                  <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none"
                    stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <polyline points="3 6 5 6 21 6"></polyline>
                    <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                  </svg>
                </button>
              </div>
            </div>
          </div>
          <!-- Sepet Alt Bilgi Paneli -->
          <div v-if="cartItems.length > 0" class="cart-panel-footer">
            <!-- İNDİRİM KODU ALANI -->
            <div class="coupon-section">
              <input v-model="couponCode" type="text" placeholder="İndirim Kodu (MILIMETRIK10)" class="coupon-input"
                :disabled="isDiscountApplied" @keyup.enter="applyCoupon" />
              <button @click="applyCoupon" class="coupon-btn" :class="{ 'applied': isDiscountApplied }">
                {{ isDiscountApplied ? 'Uygulandı' : 'Uygula' }}
              </button>
            </div>
            <p v-if="couponError" class="coupon-error">{{ couponError }}</p>
            <!-- Hesap Kartı Özet Alanı -->
            <div class="price-summary-box">
              <div class="summary-row">
                <span>Ara Toplam:</span>
                <span>{{ subTotal }} TL</span>
              </div>
              <div v-if="isDiscountApplied" class="summary-row discount-row">
                <span>İndirim (%10):</span>
                <span>-{{ discountAmount }} TL</span>
              </div>
              <div class="summary-row total-row">
                <span>Toplam Tutar:</span>
                <span class="total-amount">{{ finalTotalPrice }} TL</span>
              </div>
            </div>
            <button class="btn-checkout" @click="showCheckoutModal = true">Alışverişi Tamamla</button>
          </div>
        </div>
      </div>
    </Teleport>
  </header>
</template>
<script setup>
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue'

/// adres popup
// ⚡ [YAMA] Popup kontrol değişkeni
const showCheckoutModal = ref(false);

// (Önceki adımdan gelen müşteri verileri ref'leri kalacak)
const customerName = ref('');
const customerEmail = ref('');
const shippingAddress = ref('');

// --- REAKTİF STATE YÖNETİMİ ---
const isMenuOpen = ref(false)
const isCartOpen = ref(false)
const isSearchOpen = ref(false)
const searchText = ref('')
const activeSection = ref('hero')
const cartItems = ref([])
const isMobile = ref(false)

// Kupon Düzenekleri
const couponCode = ref('')
const isDiscountApplied = ref(false)
const couponError = ref('')

const searchInputRef = ref(null)

const toggleMenu = () => { isMenuOpen.value = !isMenuOpen.value }
const closeMenu = () => { isMenuOpen.value = false }

/// popup ile ödeme tamamlama
// ⚡ [YAMA] Popup içindeki "Ödemeye Geç" lojiği
const proceedToIyzicoPayment = async () => {
  // 1. Basit bir form kontrolü
  if (!customerName.value || !customerEmail.value || !shippingAddress.value) {
    alert("Lütfen teslimat bilgilerini eksiksiz doldurun.");
    return;
  }

  // 2. Kontrol başarılıysa, popup'ı kapatıyoruz
  showCheckoutModal.value = false;

  // 3. Bir önceki adımda yazdığımız, iyzico formunu açan handleCheckout'u tetikliyoruz.
  // Not: handleCheckout fonksiyonun artık body'ye customerName.value, customerEmail.value vs. ekleyecek şekilde güncellenmiş kalmalı!
  await handleCheckout();
};

// --- AKTİVİTE 2: AKILLI INSTANT SEARCH KONTROLLERİ ---
const openSearch = async () => {
  isSearchOpen.value = true
  await nextTick()
  if (searchInputRef.value) searchInputRef.value.focus()
}

const closeSearch = () => {
  isSearchOpen.value = false
  searchText.value = ''
  emitSearch()
}

const emitSearch = () => {
  window.dispatchEvent(new CustomEvent('global-search', { detail: searchText.value }))
}

// --- SEPET VE KUPON MATEMATİKSEL İŞLEMLERİ ---
const loadCart = () => {
  cartItems.value = JSON.parse(localStorage.getItem('cart') || '[]')
  // DÜZELTME: Eğer sepet boşaldıysa uygulanan kuponu otomatik temizle
  if (cartItems.value.length === 0) {
    isDiscountApplied.value = false
    couponCode.value = ''
  }
}

const updateQuantity = (cartId, change) => {
  const item = cartItems.value.find(p => p.cartId === cartId)
  if (item) {
    item.quantity += change
    if (item.quantity <= 0) {
      // DÜZELTME: Yanlış silmeye yol açan .id eşleşmesi .cartId olarak düzeltildi.
      cartItems.value = cartItems.value.filter(p => p.cartId !== cartId)
    }
  }
  saveCart()
}

const removeFromCart = (cartId) => {
  cartItems.value = cartItems.value.filter(p => p.cartId !== cartId)
  saveCart()
}

const saveCart = () => {
  localStorage.setItem('cart', JSON.stringify(cartItems.value))
  window.dispatchEvent(new Event('cart-updated'))
}

// Hesaplamalar (Computed)
const totalItemsCount = computed(() => cartItems.value.reduce((t, i) => t + i.quantity, 0))
const subTotal = computed(() => cartItems.value.reduce((t, i) => t + (i.price * i.quantity), 0))

const discountAmount = computed(() => {
  return isDiscountApplied.value ? Math.round(subTotal.value * 0.1) : 0
})

const finalTotalPrice = computed(() => {
  const total = subTotal.value - discountAmount.value
  return total < 0 ? 0 : total
})

// Kupon Doğrulama Lojiği
const applyCoupon = () => {
  couponError.value = ''
  if (couponCode.value.trim().toUpperCase() === 'MILIMETRIK10') {
    isDiscountApplied.value = true
  } else {
    couponError.value = 'Geçersiz indirim kodu.'
    isDiscountApplied.value = false
  }
}

const checkoutAlert = () => {
  alert(`Siparişiniz ${finalTotalPrice.value} TL üzerinden başarıyla alındı!`)
}

// --- iyzico Ödemeyi Başlat / Checkout Yaması ---
const handleCheckout = async () => {
  try {
    // 1. Sepetteki ürünlerin sadece id ve quantity bilgilerini ayıklıyoruz
    // NOT: Eğer senin sepet array'inin adı farklıysa (örn: cartItems), 'cart.value' kısmını ona göre güncelle.
    const cleanCartItems = cartItems.value.map(item => ({
      id: item.documentId || item.id,
      quantity: item.quantity
    }));

    if (cleanCartItems.length === 0) {
      alert("Sepetiniz boş! Ödeme başlatılamaz. ☕");
      return;
    }

    // 2. Strapi Backend ucumuza sepet verisini POST ediyoruz
    const response = await fetch('http://localhost:1337/api/orders', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        // Mevcut sepet elemanlarını gönderdiğiniz alan:
        items: cartItems.value,

        // ⚡ [YAMA] Backend'deki Order tablosunun beklediği yeni alanlar:
        customerName: customerName.value,
        customerEmail: customerEmail.value,
        shippingAddress: shippingAddress.value
      }),
    });

    if (!response.ok) throw new Error('Sipariş başlatılamadı.');

    const data = await response.json();

    if (data && data.checkoutFormContent) {
      // 1. Eğer ekranda eski bir modal varsa önce onu temizleyelim
      localStorage.setItem('payment_processing', 'true');
      const oldModal = document.getElementById('iyzico-modal-container');
      if (oldModal) oldModal.remove();

      // 2. Sayfanın en üstünde belirecek karartılmış arka plan (Modal Container) oluşturuyoruz
      const modalContainer = document.createElement('div');
      modalContainer.id = 'iyzico-modal-container';

      // CSS Stilleri: Sayfayı kaplasın, en üst katmanda (z-index) dursun ve ortalasın
      Object.assign(modalContainer.style, {
        position: 'fixed',
        top: '0',
        left: '0',
        width: '100vw',
        height: '100vh',
        backgroundColor: 'rgba(0, 0, 0, 0.6)', // Arkadaki elemanları karartır
        zIndex: '99999', // Sayfadaki her şeyin (Header, Slider vb.) üstüne çıkarır
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        overflowY: 'auto',
        padding: '20px'
      });

      // 3. Formun ve Kapatma Butonunun duracağı beyaz kart gövdesi (Modal Box)
      const modalBox = document.createElement('div');
      // 📱 Ekran genişliği 425px veya daha küçük mü kontrol ediyoruz
      const isMobile = window.innerWidth <= 425;

      Object.assign(modalBox.style, {
        backgroundColor: '#ffffff',
        boxShadow: '0 10px 30px rgba(0,0,0,0.3)',
        position: 'relative',
        display: 'flex',
        flexDirection: 'column',

        // ⚡ MOBİL VE MASAÜSTÜ İÇİN DİNAMİK ÖLÇÜLER:
        width: isMobile ? '100vw' : '100%',
        height: isMobile ? '100vh' : 'auto',
        maxWidth: isMobile ? '100vw' : '550px',
        maxHeight: isMobile ? '100vh' : '85vh',

        // Mobil ekranı tam kaplayacağı için yuvarlatılmış köşeleri ve padding'i mobile özel daraltıyoruz
        borderRadius: isMobile ? '0px' : '12px',
        padding: isMobile ? '15px' : '30px'
      });
      // 4. ÖDEMEYİ İPTAL ET / KAPATMA BUTONU
      const closeButton = document.createElement('button');
      closeButton.innerText = '✕ Ödemeyi İptal Et';
      Object.assign(closeButton.style, {
        alignSelf: 'flex-end',
        backgroundColor: '#faad14', // Tarık Bey'in kahve dükkanına yakışacak sıcak bir uyarı tonu
        color: '#ffffff',
        border: 'none',
        padding: '8px 16px',
        borderRadius: '6px',
        cursor: 'pointer',
        fontWeight: 'bold',
        marginBottom: '20px',
        fontSize: '14px',
        transition: 'background-color 0.2s'
      });

      // Butona basıldığında modalı tamamen DOM'dan kaldıran kapatma fonksiyonu:
      closeButton.onclick = function () {
        modalContainer.remove();
        console.log("İyzico ödeme formu kullanıcı tarafından iptal edildi.");
        localStorage.setItem('payment_processing', 'false');
      };

      // 5. İyzico'nun yerleşeceği zorunlu boş div
      const iyzicoDiv = document.createElement('div');
      iyzicoDiv.id = 'iyzipay-checkout-form';
      iyzicoDiv.className = 'responsive';

      // ⚡ DEĞİŞİKLİK BURADA: Form uzun gelirse sadece kutu içi scroll edilecek
      Object.assign(iyzicoDiv.style, {
        overflowY: 'auto', // Dikeyde taşma olursa scroll bar çıkar
        flex: '1',        // Kalan tüm boşluğu doldurmasını sağlar
        paddingRight: '5px' // Scroll çubuğu formu sıkıştırmasın diye hafif boşluk
      });
      // 6. Elemanları birbirine bağlama (DOM Tree Assembly)
      modalBox.appendChild(closeButton);
      modalBox.appendChild(iyzicoDiv);
      modalContainer.appendChild(modalBox);
      document.body.appendChild(modalContainer);

      // 7. Script enjeksiyonu ve zorla çalıştırma
      const scriptElement = document.createElement('script');
      scriptElement.type = 'text/javascript';

      const cleanScript = data.checkoutFormContent
        .replace('<script type="text/javascript">', '')
        .replace('<\/script>', '');

      scriptElement.text = cleanScript;
      modalContainer.appendChild(scriptElement);

      console.log("Ödeme modalı ve iptal butonu başarıyla enjekte edildi.");
    } else {
      alert("iyzico ödeme formu yüklenemedi.");
    }
  } catch (error) {
    console.error("Ödeme adımı tetiklenirken hata oluştu:", error);
    alert("Ödeme başlatılırken bir teknik hata oluştu.");
  }
};

// --- SCROLL & RESPONSIVE TAKİBİ ---
const scrollToSection = (e, id) => {
  e.preventDefault()
  closeMenu()
  const el = document.getElementById(id)
  if (el) {
    window.scrollTo({ top: el.getBoundingClientRect().top + window.pageYOffset - 90, behavior: 'smooth' })
    activeSection.value = id
  }
}

const checkDeviceWidth = () => {
  isMobile.value = window.innerWidth <= 768
}

let observer = null

onMounted(() => {
  loadCart()
  checkDeviceWidth()
  window.addEventListener('cart-updated', loadCart)
  window.addEventListener('resize', checkDeviceWidth)

  observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => { if (entry.isIntersecting) activeSection.value = entry.target.id })
  }, { rootMargin: '-50% 0px -50% 0px' })

  const sections = ['hero', 'about', 'coffees', 'branches']
  sections.forEach((id) => { const el = document.getElementById(id); if (el) observer.observe(el) })

  // 1. Tarayıcı hafızasında "Ödeme başlattı" imzası var mı diye bakıyoruz
  const isProcessing = localStorage.getItem('payment_processing');

  // 2. Eğer bu imza varsa, kullanıcı ödeme sayfasından geri dönmüştür
  if (isProcessing === 'true') {
    console.log("Ödeme dönüşü algılandı. Sepet ve işlem hafızası temizleniyor...");

    // Sepeti temizle
    if (typeof cartItems !== 'undefined') {
      cartItems.value = [];
    }
    localStorage.removeItem('cart');

    // 3. KRİTİK: İmzanın kendisini de silin ki sonraki her normal sayfa yenilemede sepet silinmesin!
    localStorage.removeItem('payment_processing');
  }
})

onUnmounted(() => {
  if (observer) observer.disconnect()
  window.removeEventListener('cart-updated', loadCart)
  window.removeEventListener('resize', checkDeviceWidth)
})
</script>
<style scoped>
/* --- ANA HEADER DÜZENİ --- */
.app-header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 90px;
  background-color: rgba(30, 31, 34, 0.95);
  backdrop-filter: blur(8px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  z-index: 999;
  display: flex;
  align-items: center;
}

.header-container {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo a {
  font-family: var(--font-serif);
  font-size: 1.3rem;
  font-weight: 600;
  color: #ffffff;
  text-decoration: none;
  letter-spacing: 0.05em;
}

.desktop-menu {
  display: flex;
  gap: 32px;
}

.nav-link {
  font-family: var(--font-sans);
  font-size: 1rem;
  color: rgba(255, 255, 255, 0.75);
  text-decoration: none;
  transition: color 0.2s ease;
}

.nav-link.active,
.nav-link:hover {
  color: var(--color-gold);
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 20px;
}

.action-trigger-btn {
  background: none;
  border: none;
  color: #ffffff;
  cursor: pointer;
  display: flex;
  align-items: center;
  transition: color 0.2s ease;
}

.action-trigger-btn:hover {
  color: var(--color-gold);
}

/* --- ARAMA ÇUBUĞU TASARIMI --- */
.instant-search-wrapper {
  flex: 1;
  max-width: 480px;
  margin: 0 32px;
  position: relative;
  display: flex;
  align-items: center;
}

.search-input {
  width: 100%;
  background-color: rgba(255, 255, 255, 0.04);
  border: 1px solid rgba(226, 186, 115, 0.25);
  padding: 10px 40px 10px 18px;
  color: #ffffff;
  border-radius: 30px;
  outline: none;
  font-family: var(--font-sans);
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.search-input:focus {
  border-color: var(--color-gold);
  background-color: rgba(0, 0, 0, 0.5);
  box-shadow: 0 0 14px rgba(226, 186, 115, 0.15);
}

.search-close-inline {
  position: absolute;
  right: 16px;
  background: none;
  border: none;
  color: rgba(255, 255, 255, 0.4);
  cursor: pointer;
  font-size: 0.85rem;
}

.search-close-inline:hover {
  color: var(--color-gold);
}

/* --- SEPET İKONU VE ROZET --- */
.cart-btn {
  background: none;
  border: none;
  color: var(--color-gold);
  cursor: pointer;
  position: relative;
  display: flex;
  align-items: center;
}

.cart-badge {
  position: absolute;
  top: -6px;
  right: -8px;
  background-color: var(--color-gold);
  color: #1E1F22;
  font-family: var(--font-sans);
  font-size: 0.72rem;
  font-weight: 700;
  width: 18px;
  height: 18px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* --- MOBİL MENÜ TETİKLEYİCİLERİ --- */
.hamburger-btn {
  display: none;
  background: none;
  border: none;
  cursor: pointer;
  flex-direction: column;
  justify-content: space-between;
  width: 24px;
  height: 16px;
  padding: 0;
}

.hamburger-btn .bar {
  width: 100%;
  height: 2px;
  background-color: #ffffff;
  transition: all 0.25s ease;
}

.hamburger-btn.is-active .bar:nth-child(1) {
  transform: translateY(7px) rotate(45deg);
  background-color: var(--color-gold);
}

.hamburger-btn.is-active .bar:nth-child(2) {
  opacity: 0;
}

.hamburger-btn.is-active .bar:nth-child(3) {
  transform: translateY(-7px) rotate(-45deg);
  background-color: var(--color-gold);
}

.mobile-sidebar {
  position: fixed;
  top: 0;
  right: -300px;
  width: 300px;
  height: 100vh;
  background-color: #1E1F22;
  box-shadow: -10px 0 30px rgba(0, 0, 0, 0.5);
  z-index: 1000;
  padding: 120px 40px;
  transition: transform 0.4s cubic-bezier(0.16, 1, 0.3, 1);
}

.mobile-sidebar.open {
  transform: translateX(-300px);
}

.mobile-nav-links {
  display: flex;
  flex-direction: column;
  gap: 28px;
}

.mobile-link {
  font-family: var(--font-serif);
  font-size: 1.4rem;
  color: #ffffff;
  text-decoration: none;
}

.mobile-link.active {
  color: var(--color-gold);
}

.sidebar-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 998;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s ease;
}

.sidebar-overlay.visible {
  opacity: 1;
  pointer-events: auto;
}

/* --- YANDAN AÇILAN SEPET PANELİ CSS --- */
.cart-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(30, 31, 34, 0);
  z-index: 99999;
  display: flex;
  justify-content: flex-end;
  pointer-events: none;
  backdrop-filter: blur(0px);
  transition: all 0.4s ease;
}

.cart-backdrop.is-visible {
  background-color: rgba(30, 31, 34, 0.65);
  pointer-events: auto;
  backdrop-filter: blur(4px);
}

.cart-panel {
  width: 100%;
  max-width: 440px;
  height: 100vh;
  background-color: #2C221E;
  box-shadow: -16px 0 48px rgba(0, 0, 0, 0.6);
  display: flex;
  flex-direction: column;
  transform: translateX(100%);
  transition: transform 0.4s cubic-bezier(0.16, 1, 0.3, 1);
}

.cart-panel.is-open {
  transform: translateX(0);
}

.cart-panel-header {
  padding: 28px 32px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.06);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.cart-panel-title {
  font-family: var(--font-serif);
  font-size: 1.3rem;
  color: #ffffff;
  font-weight: 500;
}

.cart-close-btn {
  background: none;
  border: none;
  color: rgba(255, 255, 255, 0.4);
  cursor: pointer;
  display: flex;
  align-items: center;
  transition: color 0.2s ease;
}

.cart-close-btn:hover {
  color: var(--color-gold);
}

.cart-panel-body {
  flex: 1;
  overflow-y: auto;
  padding: 32px;
}

.empty-cart-message {
  text-align: center;
  padding: 80px 0;
  display: flex;
  flex-direction: column;
  gap: 12px;
  align-items: center;
  color: #b0b0b0;
}

.empty-icon {
  font-size: 2.5rem;
}

.cart-items-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

/* Ürün Kartı İç Düzenleri */
.cart-item-card {
  display: flex;
  gap: 16px;
  align-items: center;
  background-color: rgba(255, 255, 255, 0.02);
  padding: 14px;
  border-radius: 12px;
  border: 1px solid rgba(255, 255, 255, 0.04);
  position: relative;
}

.cart-item-img {
  width: 70px;
  height: 70px;
  object-fit: cover;
  border-radius: 8px;
}

.cart-item-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.cart-item-name {
  font-family: var(--font-serif);
  font-size: 1rem;
  color: #ffffff;
  font-weight: 500;
  line-height: 1.3;
}

.cart-item-grind {
  font-family: var(--font-sans);
  font-size: 0.75rem;
  color: #b0b0b0;
  font-style: italic;
}

.cart-item-price {
  font-family: var(--font-sans);
  font-size: 0.9rem;
  color: var(--color-gold);
  font-weight: 600;
}

.quantity-control {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-top: 6px;
}

.qty-btn {
  background: rgba(255, 255, 255, 0.06);
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: #ffffff;
  width: 24px;
  height: 24px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.85rem;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: all 0.2s ease;
}

.qty-btn:hover {
  background-color: var(--color-gold);
  color: #1E1F22;
  border-color: var(--color-gold);
}

.qty-value {
  color: #ffffff;
  font-family: var(--font-sans);
  font-size: 0.85rem;
  font-weight: 600;
  min-width: 14px;
  text-align: center;
}

.cart-item-remove-btn {
  background: none;
  border: none;
  color: rgba(255, 255, 255, 0.25);
  cursor: pointer;
  padding: 6px;
  display: flex;
  align-items: center;
  border-radius: 6px;
  transition: all 0.2s ease;
}

.cart-item-remove-btn:hover {
  color: #e57373;
  background-color: rgba(229, 115, 115, 0.08);
}

/* --- KUPON & SEPET ALTI HESAP TABLOSU --- */
.cart-panel-footer {
  padding: 24px 32px;
  border-top: 1px solid rgba(255, 255, 255, 0.06);
  background-color: rgba(0, 0, 0, 0.18);
}

.coupon-section {
  display: flex;
  gap: 10px;
  margin-bottom: 16px;
}

.coupon-input {
  flex: 1;
  background-color: rgba(255, 255, 255, 0.04);
  border: 1px solid rgba(255, 255, 255, 0.12);
  color: #ffffff;
  padding: 10px 14px;
  border-radius: 6px;
  outline: none;
  font-family: var(--font-sans);
  font-size: 0.85rem;
  transition: border-color 0.2s ease;
}

.coupon-input:focus {
  border-color: var(--color-gold);
}

.coupon-input:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.coupon-btn {
  background-color: rgba(226, 186, 115, 0.12);
  border: 1px solid var(--color-gold);
  color: var(--color-gold);
  padding: 0 16px;
  border-radius: 6px;
  cursor: pointer;
  font-family: var(--font-sans);
  font-size: 0.85rem;
  font-weight: 500;
  transition: all 0.2s ease;
}

.coupon-btn:hover {
  background-color: var(--color-gold);
  color: #1E1F22;
}

.coupon-btn.applied {
  background-color: #2e7d32;
  border-color: #2e7d32;
  color: #ffffff;
  cursor: not-allowed;
}

.coupon-error {
  color: #e57373;
  font-size: 0.75rem;
  font-family: var(--font-sans);
  margin-top: -10px;
  margin-bottom: 12px;
}

.price-summary-box {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
}

.summary-row {
  display: flex;
  justify-content: space-between;
  font-family: var(--font-sans);
  font-size: 0.95rem;
  color: rgba(255, 255, 255, 0.65);
}

.discount-row {
  color: #81c784;
  font-weight: 500;
}

.total-row {
  border-top: 1px solid rgba(255, 255, 255, 0.06);
  padding-top: 12px;
  color: #ffffff;
}

.total-amount {
  font-size: 1.35rem;
  font-weight: 700;
  color: var(--color-gold);
}

.btn-checkout {
  width: 100%;
  background-color: var(--color-gold);
  color: #1E1F22;
  font-family: var(--font-sans);
  font-weight: 600;
  padding: 14px 0;
  border: none;
  cursor: pointer;
  border-radius: 4px;
  font-size: 0.95rem;
  transition: all 0.2s ease;
}

.btn-checkout:hover {
  background-color: #ffffff;
}

/* --- MOBIL RESPONSIVE KURALLARI --- */
@media (max-width: 980px) {
  .desktop-menu {
    display: none;
  }

  .hamburger-btn {
    display: flex;
  }
}

@media (max-width: 768px) {
  .instant-search-wrapper {
    margin: 0 12px;
    max-width: 100%;
  }

  .cart-panel {
    max-width: 100%;
  }
}
</style>