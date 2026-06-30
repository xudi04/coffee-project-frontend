<template>
  <section class="products-section">
    <div class="container">
      
      <div class="section-header">
        <span class="section-subtitle">TAZE KAVRULMUŞ ÇEKİRDEKLER</span>
        <h2 class="section-title">Nitelikli Seçki</h2>
      </div>

      <div class="filter-tabs">
        <button 
          v-for="tab in categories" 
          :key="tab.slug"
          class="tab-btn"
          :class="{ active: activeCategory === tab.slug }"
          @click="activeCategory = tab.slug"
        >
          {{ tab.name }}
        </button>
      </div>

      <div class="products-grid">
        <div 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="product-card"
        >
          <img :src="product.image" :alt="product.name" class="card-img" />
          
          <div class="card-overlay">
            <span class="coffee-meta">{{ product.origin }} | {{ product.process }}</span>
            <h3 class="coffee-name">{{ product.name }}</h3>
            
            <div class="coffee-specs">
              <div class="spec-item">
                <span>Gövde:</span> 
                <div class="bar"><span class="fill" :style="{ width: product.body + '%' }"></span></div>
              </div>
              <div class="spec-item">
                <span>Asidite:</span> 
                <div class="bar"><span class="fill" :style="{ width: product.acidity + '%' }"></span></div>
              </div>
              <div class="spec-item">
                <span>Sertlik:</span> 
                <div class="bar"><span class="fill" :style="{ width: product.roast + '%' }"></span></div>
              </div>
              <span class="taste-profile">Tat Profili: {{ product.tasteProfile }}</span>
            </div>
            
            <div class="grind-selector-wrapper">
              <select v-model="selectedGrinds[product.id]" class="grind-select" aria-label="Öğütme Derecesi">
                <option value="Çekirdek">☕ Çekirdek (Öğütmesiz)</option>
                <option value="Espresso">⚡ Espresso</option>
                <option value="Filtre Kahve (Kağıt)">⏳ Filtre Kahve (Kağıt)</option>
                <option value="Moka Pot">🇮🇹 Moka Pot</option>
                <option value="Türk Kahvesi">🇹🇷 Türk Kahvesi</option>
              </select>
            </div>
            
            <div class="card-footer">
              <span class="price">{{ product.price }} TL</span>
              <button 
                class="btn-add-cart" 
                :class="{ 'is-added': addingStatus[product.id] }"
                @click="addToCart(product)"
                :disabled="addingStatus[product.id]"
              >
                {{ addingStatus[product.id] ? 'Eklendi ✓' : 'Sepete Ekle +' }}
              </button>
            </div>
          </div>
        </div>
      </div>

    </div>
  </section>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const isLoading = ref(false)
// --- REAKTİF STATE TANIMLARI ---
const activeCategory = ref('all')
const searchQuery = ref('')
const addingStatus = ref({})

// Her kartın kendi öğütme seçimini hafızada tutması için dynamic object map
const selectedGrinds = ref({
  1: 'Çekirdek',
  2: 'Çekirdek',
  3: 'Çekirdek',
  4: 'Çekirdek',
  5: 'Çekirdek'
})

const products = ref([])

const STRAPI_BASE_URL = 'http://localhost:1337'

const fetchProducts = async () => {
  try {
    const response = await fetch(`${STRAPI_BASE_URL}/api/products?populate=*`)
    if (!response.ok) throw new Error(`HTTP hata: ${response.status}`)
    
    const json = await response.json()
    const rawData = json.data || []
    
    products.value = rawData.map(item => {
      const fields = item.attributes ? item.attributes : item
      const imgData = fields.image?.data?.attributes || fields.image
      const imageUrl = imgData?.url ? `${STRAPI_BASE_URL}${imgData.url}` : 'https://via.placeholder.com/400x500'
      console.log(item.documentId);
      
      return {
        id: item.documentId,
        name: fields.name,
        price: Number(fields.price),
        category: fields.category,
        image: imageUrl,
        acidity: Number(fields.acidity) || 50,
        body: Number(fields.body) || 50,
        roast: Number(fields.roast) || 50,
        tasteProfile: fields.tasteProfile || ''
      }
    })
  } catch (error) {
    console.error("Strapi'den kahveler yüklenirken hata oluştu:", error)
  }
}

const categories = ref([
  { name: 'Hepsi', slug: 'all' },
  { name: 'Single Origin (Tek Köken)', slug: 'single_origin' },
  { name: 'Blend (Harman)', slug: 'blend' },
  { name: 'Filtre Kahve', slug: 'filtre_kahve' },
  { name: 'Espresso', slug: 'espresso' }
])

// --- AKTİVİTE 2: HEM KATEGORİ HEM INSTANT SEARCH BİRLEŞİK SÜZGECİ ---
const filteredProducts = computed(() => {
  return products.value.filter(product => {
    const matchesCategory = activeCategory.value === 'all' || product.category === activeCategory.value
    const matchesSearch = product.name.toLowerCase().includes(searchQuery.value.toLowerCase()) || 
                          product.origin.toLowerCase().includes(searchQuery.value.toLowerCase())
    return matchesCategory && matchesSearch
  })
})

// --- SEPETE EKLEME VE ÖĞÜTME TİPİ AYRIŞTIRMA LOJİĞİ ---
const addToCart = (product) => {
  const currentCart = JSON.parse(localStorage.getItem('cart') || '[]')
  const method = selectedGrinds.value[product.id] || 'Çekirdek'
  
  // Aynı kahve farklı öğütme derecesiyle gelirse ayrı satır olması için benzersiz kombine ID üretimi
  const uniqueCartId = `${product.id}_${method.replace(/\s+/g, '')}`
  
  const existingItem = currentCart.find(item => item.cartId === uniqueCartId)
  
  if (existingItem) {
    existingItem.quantity += 1
  } else {
    currentCart.push({
      cartId: uniqueCartId,
      id: product.id,
      name: product.name,
      price: product.price,
      image: product.image,
      grindOption: method,
      quantity: 1
    })
  }
  
  localStorage.setItem('cart', JSON.stringify(currentCart))
  window.dispatchEvent(new Event('cart-updated'))

  // Buton Feedback Aktivitesi
  addingStatus.value[product.id] = true
  setTimeout(() => { addingStatus.value[product.id] = false }, 1000)
}

// Arama kutusundan fırlatılacak sinyali yakalama
const handleSearchGlobal = (e) => {
  searchQuery.value = e.detail || ''
}

onMounted(() => {
  window.addEventListener('global-search', handleSearchGlobal)
  fetchProducts()
})
onUnmounted(() => {
  window.removeEventListener('global-search', handleSearchGlobal)
})
</script>

<style scoped>
.products-section { width: 100%; background-color: #FBF9F6; padding: 100px 0; color: var(--text-dark); }
.section-header { text-align: center; margin-bottom: 48px; }
.section-subtitle { font-family: var(--font-sans); font-size: 0.9rem; font-weight: 600; color: var(--color-gold); letter-spacing: 0.15em; display: block; margin-bottom: 12px; }
.section-title { font-family: var(--font-serif); font-size: 2.5rem; color: #1e1f22; font-weight: 600; }
.filter-tabs { display: flex; justify-content: center; gap: 32px; margin-bottom: 64px; flex-wrap: wrap; }
.tab-btn { background: none; border: none; font-family: var(--font-sans); font-size: 1rem; color: #7a7a7a; cursor: pointer; padding: 8px 4px; position: relative; transition: all 0.3s ease; }
.tab-btn:hover, .tab-btn.active { color: #1e1f22; }
.tab-btn::after { content: ''; position: absolute; bottom: 0; left: 0; width: 0; height: 2px; background-color: var(--color-gold); transition: width 0.3s ease; }
.tab-btn.active::after { width: 100%; }
.products-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 30px; }
.product-card { position: relative; border-radius: 24px; overflow: hidden; aspect-ratio: 3 / 4.3; box-shadow: 0 12px 32px rgba(0, 0, 0, 0.05); background-color: #ffffff; transition: transform 0.3s ease, box-shadow 0.3s ease; }
.product-card:hover { transform: translateY(-5px); box-shadow: 0 20px 40px rgba(0, 0, 0, 0.12); }
.card-img { width: 100%; height: 100%; object-fit: cover; }
.card-overlay { position: absolute; bottom: 0; left: 0; width: 100%; background: linear-gradient(180deg, rgba(0,0,0,0) 0%, rgba(30, 31, 32, 0.98) 32%); padding: 24px; z-index: 1; color: #ffffff; display: flex; flex-direction: column; }
.coffee-meta { font-family: var(--font-sans); font-size: 0.75rem; color: #b0b0b0; margin-bottom: 6px; }
.coffee-name { font-family: var(--font-serif); font-size: 1.35rem; font-weight: 500; margin-bottom: 12px; }
.coffee-specs { display: flex; flex-direction: column; gap: 6px; border-bottom: 1px solid rgba(255, 255, 255, 0.08); padding-bottom: 12px; margin-bottom: 12px; }
.spec-item { display: flex; align-items: center; justify-content: space-between; font-family: var(--font-sans); font-size: 0.7rem; color: #d1d1d1; }
.spec-item span { width: 50px; }
.bar { flex: 1; height: 3px; background-color: rgba(255, 255, 255, 0.15); margin-left: 12px; border-radius: 2px; overflow: hidden; }
.fill { display: block; height: 100%; background-color: var(--color-gold); }
.taste-profile { font-family: var(--font-sans); font-size: 0.7rem; color: var(--color-gold); margin-top: 4px; }

/* DROPDOWN ÖZEL ASALET STİLLERİ */
.grind-selector-wrapper {
  margin-bottom: 16px;
  width: 100%;
}
.grind-select {
  width: 100%;
  background-color: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.15);
  color: #ffffff;
  font-family: var(--font-sans);
  font-size: 0.8rem;
  padding: 8px 12px;
  border-radius: 6px;
  outline: none;
  cursor: pointer;
  transition: all 0.2s ease;
}
.grind-select:focus {
  border-color: var(--color-gold);
  background-color: rgba(30, 31, 34, 0.95);
}
.grind-select option {
  background-color: #2C221E;
  color: #ffffff;
}

.card-footer { display: flex; align-items: center; justify-content: space-between; }
.price { font-family: var(--font-sans); font-size: 1.2rem; font-weight: 600; }
.btn-add-cart { background: rgba(255, 255, 255, 0.06); border: 1px solid rgba(255, 255, 255, 0.2); padding: 8px 16px; border-radius: 8px; color: #ffffff; font-family: var(--font-sans); font-size: 0.85rem; cursor: pointer; transition: all 0.2s ease; }
.btn-add-cart:hover { background-color: var(--color-gold); border-color: var(--color-gold); color: #1e1f22; }
.btn-add-cart.is-added { background-color: #2e7d32; border-color: #2e7d32; color: #ffffff; cursor: not-allowed; }

@media (max-width: 1024px) { .products-grid { grid-template-columns: repeat(2, 1fr); } }
@media (max-width: 768px) { .products-grid { grid-template-columns: 1fr; max-width: 400px; margin: 0 auto; } }
</style>