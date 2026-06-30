<template>
  <section class="hero-section" id="hero">
    <img 
      src="https://images.unsplash.com/photo-1501339847302-ac426a4a7cbb?q=80&w=1920&auto=format&fit=crop" 
      alt="Tarık's Coffee Roasters Atmosfer" 
      class="hero-bg-img"
    />

    <div class="hero-overlay"></div>

    <div class="container hero-container">
      <div class="hero-content">
        <span class="hero-subtitle">NİTELİKLİ KAHVE ATÖLYESİ</span>
        <h1 class="hero-title">Atölyemizden Fincanınıza, Kusursuz Bir Deneyim</h1>
        
        <div class="hero-actions">
          <a href="#" class="btn-primary">Çekirdekleri Keşfet</a>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
</script>

<style scoped>
.hero-section {
  position: relative;
  /* vw yerine %100 kullanarak sağa taşma sorununu kökten çözüyoruz */
  width: 100%; 
  height: calc(100vh + 90px); 
  display: flex;
  align-items: center;
  justify-content: center;
  /* İçerideki absolute görselin dışarı taşmasını engellemek için */
  overflow: hidden; 
}

/* HTML Görselinin CSS ile Arka Plan Gibi Davranmasını Sağlama */
.hero-bg-img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  /* Tasarımdaki background-size: cover mantığının HTML img karşılığı */
  object-fit: cover; 
  object-position: center;
  z-index: 0;
  pointer-events: none; /* Kullanıcının görseli yanlışlıkla sürüklememesi için */
}

/* Tasarımdaki o loş, premium kahve dükkanı havasını veren karartma katmanı */
.hero-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(
    180deg, 
    rgba(30, 31, 34, 0.85) 0%, 
    rgba(20, 15, 12, 0.75) 50%, 
    rgba(30, 31, 34, 0.9) 100%
  );
  z-index: 1;
}

.hero-container {
  position: relative;
  z-index: 2; /* Görsel ve overlay katmanlarının üzerine çıkartıyoruz */
  width: 100%;
  display: flex;
  justify-content: center;
}

.hero-content {
  width: 100%;
  max-width: 950px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.hero-subtitle {
  font-family: var(--font-sans);
  font-size: 1rem;
  font-weight: 600;
  color: var(--color-gold);
  letter-spacing: 0.2em;
  margin-bottom: 24px;
  text-transform: uppercase;
}

.hero-title {
  font-family: var(--font-serif);
  font-size: 3.5rem;
  font-weight: 600;
  color: #ffffff;
  line-height: 1.25;
  margin-bottom: 48px;
  text-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

.btn-primary {
  display: inline-block;
  font-family: var(--font-serif);
  font-size: 1.25rem;
  font-weight: 500;
  color: var(--color-gold);
  text-decoration: none;
  padding: 14px 42px;
  border: 1px solid var(--color-gold);
  background: transparent;
  transition: var(--transition-smooth);
}

.btn-primary:hover {
  background-color: var(--color-gold);
  color: var(--bg-antrasit);
  box-shadow: 0 0 20px rgba(223, 183, 108, 0.3);
  transform: translateY(-2px);
}

/* Responsive Kuralları */
@media (max-width: 1024px) {
  .hero-title { font-size: 2.8rem; margin-bottom: 36px; }
}

@media (max-width: 768px) {
  .hero-subtitle { font-size: 0.85rem; letter-spacing: 0.15em; }
  .hero-title { font-size: 2rem; line-height: 1.3; }
  .btn-primary { padding: 12px 32px; font-size: 1.1rem; }
}
</style>